# Mail Service
Rails에서는 기본으로 제공해주는 `ActionMailer`와 다양한 mailer 관련 gem이 있다.
[진식님 블로그](http://destiny738.tistory.com/552) 참조

## 사용자에게 이메일 문의 받기
사용자에게 받은 문의 내용이 회사의 메일로 전송되도록 하자.
[MailForm](https://github.com/plataformatec/mail_form) gem을 이용하면 `Form`으로부터 directly 메일을 보낼 수 있다.

### 1. installation
`Gemfile`에 아래를 추가한다.
```ruby
gem 'mail_form'
```

`bundle install`

### 2. model 생성
`MailForm::Base`를 상속받는 Contact 모델을 작성한다. 

```ruby
class ContactForm < MailForm::Base
  attribute :name,      :validate => true
  attribute :email,     :validate => /\A([\w\.%\+\-]+)@([\w\-]+\.)+([\w]{2,})\z/i
  attribute :file,      :attachment => true

  attribute :message
  attribute :nickname,  :captcha  => true

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

**attributes**
모델 클래스에 선언된 `attribute`는 이메일에 포함된다.

* :validate 
    * 해당 옵션을 가지면 presence를 검증
    * regexp를 받으면 정규표현식 검증
    * array를 받으면 array에 포함되는지 검증
* :attachment
    - 해당 옵션을 가지면 이메일에 첨부되는 파일로 인식한다
    - contact form에 `multitype` 속성을 부여해야 함
* :captcha
    - 해당 옵션을 가지면 blank여야만 vaildate 된다.
    - 스팸, 봇 공격 위험이 있을 때 해당 field를 hidden 시키고 사용한다.

**append**
MailForm은 user가 직접 입력하기 어려운 버전 정보들도 쉽게 append 할 수 있다.

```ruby
class ContactForm < MailForm::Base
  append :remote_ip, :user_agent, :session
  # ...
end
```

### 3. controller 작성


## Action Mailer를 통한 이메일 전송
Rails는 메일 전송을 위한 module로 `ActionMailer`를 제공한다.
`ActionMailer`는 요청에 대해 HTTP가 아닌 SMTP(simple mail transfer protocol) 로 응답한다.