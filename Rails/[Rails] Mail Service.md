# Mail Service
Rails에서는 기본으로 제공해주는 `ActionMailer`와 이를 기반으로 한 다양한 gem이 있다.
`ActionMailer`는 요청에 대해 HTTP가 아닌 SMTP(simple mail transfer protocol) 로 응답한다.

## 메일을 보내기 위한 설정
`ActionMailer`를 사용하기 위해서는 `config`에서 메일 송신과 관련한 설정을 해줘야 한다.
```ruby
config.action_mailer.delivery_method = :smtp
config.action_mailer.default_url_options = {host: 'http://fanmade.kr'}
config.action_mailer.smtp_settings = {
      :address              => "smtp.gmail.com",
      :port                 => 587,
      :domain               => "http://fanmade.kr",
      :user_name            => "your_email@gmail.com",
      :password             => "your_password",
      :authentication       => "plain",
      :enable_starttls_auto => true
}
```

* **delivery_method** - 메일 전송 방법 옵션을 지정하며 default는 :smtp
* **default_url_option** - 메일에서는 url을 부를 때 반드시 host를 설정해줘야 한다. 여기서 default host를 지정할 수 있다.
* **smtp_settings** - :stmp와 관련한 설정값
    - :address - 메일 서버의 주소
    - :port - 메일 서버가 사용하는 포트 번호
    - :user_name - 메일 서버에서 인증이 필요한 경우의 사용자이름
    - :password - 메일 서버에서 인증이 필요한 경우의 비밀번호
    - :authentication - 메일 서버의 인증 종류

여기서는 Gmail에서 지원하는 smtp 서버를 사용하였다!

> 노트! 메일 서버란? 
> 수신되는 전자우편을 받아주고 송신되는 전자우편을 배달하기 위해 전달해주는 역할을 하는 일종의 응용프로그램을 가리킨다.

자세한 것은 [Action Mailer Configuration](http://guides.rubyonrails.org/action_mailer_basics.html#action-mailer-configuration) 참조

## 사용자에게 이메일 문의 받기
사용자에게 받은 문의 내용이 회사의 메일로 전송되도록 하자.
[MailForm](https://github.com/plataformatec/mail_form) gem을 이용하면 `Form`으로부터 directly 메일을 보낼 수 있다.

### 1. Installation
`Gemfile`에 아래를 추가한다.
```ruby
gem 'mail_form'
```

`bundle install`

### 2. Model
`MailForm::Base`를 상속받는 Contact 모델을 작성한다. 

```ruby
class Contact < MailForm::Base
  attribute :name,      :validate => true
  attribute :email,     :validate => /\A([\w\.%\+\-]+)@([\w\-]+\.)+([\w]{2,})\z/i
  attribute :file,      :attachment => true
  attribute :message,
  attribute :nickname  :captcha  => true

  # Declare the e-mail headers. It accepts anything the mail method
  # in ActionMailer accepts.
  def headers
    {
      :subject => "My Contact Form",
      :to => "your.email@your.domain.com",
      :from => %("#{name}" <#{email}>)
    }
  end
end
```

#### attributes
모델 클래스에 선언된 `attribute`는 이메일에 포함된다.

* :validate 
    * 해당 옵션을 가지면 presence를 검증
    * regexp를 받으면 정규표현식 검증
    * array를 받으면 array에 포함되는지 검증
* :attachment
    - 해당 옵션을 가지면 이메일에 첨부되는 파일로 인식한다
    - contact form에 `multipart` 속성을 부여해야 함
* :captcha
    - 해당 옵션을 가지면 blank여야만 vaildate 된다.
    - 스팸, 봇 공격 위험이 있을 때 해당 field를 hidden 시키고 사용하자.

####append
MailForm은 user가 직접 입력하기 어려운 버전 정보들도 쉽게 append 할 수 있다.

```ruby
class ContactForm < MailForm::Base
  append :remote_ip, :user_agent, :session
  # ...
end
```

### 3. Controller
```ruby
class ContactsController < ApplicationController
  def new
    @contact = Contact.new
  end

  def create
    @contact = Contact.new(params[:contact])
    @contact.request = request
    if @contact.deliver
      redirect_to recruits_path, notice: '지원서가 접수되었습니다'
    else
      render :new
    end
  end
end
```

### 4. Route
```ruby
resources :contact, only: [:new, :create]
```

### 5. View
```rhtml
<%= form_for @contact, html: {multipart: :true} do |f| %>
    <% if @contact.errors.any? %>
        <div id="error_explanation">
            <% @contact.errors.full_messages.each do |message| %>
                <div><%= message %></div>
            <% end %>
        </div>
    <% end %>

    <div class="field">
        <%= f.label :title, '제목' %><br>
        <%= f.text_field :title, required: true %>
    </div>
    <div class="field">
        <%= f.label :email, '메일' %><br>
        <%= f.text_field :email, required: true %>
    </div>
    <div class="field">
        <%= f.label :message, '내용' %><br>
        <%= f.text_area :message, required: true %>
    </div>

    <div class="field">
        <%= f.label :file, '파일' %><br>
        <%= f.file_field :file, required: true %>
        <p>zip 파일로 보내주세요</p>
    </div>

    <%= f.submit :submit, value: '지원하기', id: 'submit' %>
<% end %>
```

> 파일을 첨부하려면 multipart 옵션을 지정해야 한다.

## Action Mailer를 통한 이메일 전송
`ActionMailer`를 이용하여 Event-driven으로 사용자의 행동에 따른 메일링 시스템을 구축할 수 있다.
사용자가 회원가입을 할 때, 자동으로 환영 메일을 보내는 시스템을 만들어보자!

### 1. Mailer 생성
`Mailer`도 `Controller`처럼 요청을 받아 필요한 처리를 하고 그 결과를 템플릿을 사용해 메일 문서로 작성한다.
Generator를 통해 메일러를 생성할 수 있다. 
```bash
rails g mailer [Name]
```

Generator를 사용하고 싶지 않은 경우에는 `app/mailers` 폴더에 파일을 생성하고 `ActionMailer::Base`를 상속해주자.

```ruby
class UserMailer < ActionMailer::Base
end
```

### 2. Mailer 작성
생성한 `Mailer`에서 `welcome_email` 메소드를 추가해보자.
```ruby
class UserMailer < ActionMailer::Base
    default from: 'contact@fanmade.kr'
    layout 'mailer'

    def welcome_email(user)
        @user = user
        mail(to: @user.email, subject: '팬메이드에 가입하신 것을 환영합니다')
    end
end
```

* **default** - 메일러에서 전송되는 모든 메일의 기본값으로 `:from` 헤더를 설정
* **layout** - `view/layout/mailer.html.erb` 파일을 layout으로 설정
* **mail** - 실제 메일을 보내게 되는 함수이다. `:to`와 `:subject` 헤더를 설정

## 3. View 생성
`user_mailer/welcome_email.html.erb`에 view를 생성한다.
`Controller`와 마찬가지로 `Mailer`에서 생성된 인스턴스 변수를 사용할 수 있다.

user_mailer/welcome_email.html.erb:
```ruby
<h1><%= @user.nickname %>님</h1>
<div class="article" style="margin-top: 4px;">가입을 환영합니다!</div>
```

#### url 사용하기
`Mailer`는 HTTP 요청을 사용하지 않기 때문에 url과 같이 어플리케이션의 host 정보를 사용하고 싶은 경우에는 `host` 옵션을 지정해야한다.
매번 `host`를 지정해주면 번거롭기 때문에 `config`에서 설정한다.
```ruby
config.action_mailer.default_url_options = {host: 'http://fanmade.kr'}
``` 

이를 설정하지 않으면 `url_for`에 `:host` 옵션을 명시적으로 넘겨야 한다.
```rhtml
<%= url_for(host: 'example.com', controller: 'welcome', action: 'greeting') %>
```

`link_to`를 사용한다면 `*_path` 헬퍼 대신 `*_url` 헬퍼를 사용해야 한다.
```rhtml
<%= link_to '어서오세요', welcome_url %>
```

#### 파일 첨부
`Mailer`에서 아래와 같이 설정하면 파일을 첨부할 수 있다.
```ruby
attachments['filename.pdf'] = File.read('/path/to/filename.pdf')
```

메일 내부에 인라인으로 첨부하고 싶으면 아래와 같이 사용한다.
```ruby
attachments.inline['mail_img.png'] = File.read('app/assets/images/mail_img.png')
```

```rhtml
<%= image_tag attachments['mail_img.png'].url, class: 'photos' %>
```

## 4. Mailer 호출
`Mailer`를 실행하려면 컨트롤러에서 호출해줘야한다.
```ruby
UserMailer.welcome_email(@user).deliver
```

예제에서 가입에 성공하였을 때 메일을 전송하도록 컨트롤러에서 `Mailer`를 호출하면 된다.
devise의 registration controller를 override 시켜 Mailer를 실행시킨다.
```ruby
class RegistrationsController < Devise::RegistrationsController
    def create
        super
        UserMailer.welcome_email(resource).deliver unless resource.invalid?
    end
    ...중략...
end
```

#### 비동기 전송

이메일을 보낼 때 서버에서 많은 양의 데이터를 처리해야 한다. 때문에 사용자가 메일이 보내지기까지 기다려야 하는 문제가 있다.
`Action Mailer`는 rails의 `Action Job`과 결합하여 Web의 요청/응답 흐름의 바깥에서 비동기로 메일을 전송할 수 있다.
```ruby
UserMailer.welcome_email(@user).deliver_later
```

또는 비동기 메일 전송을 위한 다양한 gem이 존재하는데, ohouse에서는 비동기 작업을 처리하는 [delayed_job_active_record](https://github.com/collectiveidea/delayed_job)를 사용했다.

<br><br><br>
[진식님 블로그](http://destiny738.tistory.com/552) 참조
[Rails Guide](http://guides.rorlab.org/action_mailer_basics.html) 참조