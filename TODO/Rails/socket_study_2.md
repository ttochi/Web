# Socket Study 2
rails 5에서 Devise gem을 이용한 회원 관리 및 기본적인 DB를 다루어봅니다.
프로젝트에 본격적으로 들어가기 앞서 Ajax를 이용한 채팅서비스를 제작해봐요~

## layout 설정

### Sass?
Sass는 CSS 상위에 있는 meta-language로 보다 간결한 CSS 문법을 사용한다. 
DRY(Don’t Repeat Yourself) 원칙을 적용해서 더 빠르고 효율적으로 코드를 작성하고 쉽게 유지보수 하도록 해준다.

`app/assets/stylesheets` 디렉토리에서 CSS관련 파일을 관리할겁니다.
`application.css`를 `application.css.scss`로 바꿔주세요.

bootstrap gem
```ruby
gem 'bootstrap-sass'
```

```scss
$light-sky: #d8f7ff;
$navbar-default-color: $light-sky;
$navbar-default-bg: #293370;
$navbar-default-link-color: gray;
$navbar-default-link-active-color: $light-sky;
$navbar-default-link-hover-color: white;
$navbar-default-link-hover-bg: black;

@import "bootstrap";

body { padding-top: 60px; }
```

### layout
```rhtml
<body>
<!-- Fixed navbar -->
<div class="navbar navbar-default navbar-fixed-top" role="navigation">
    <div class="container">
        <div class="navbar-header">
            <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                <span class="sr-only">Toggle navigation</span>
                <span class="icon-bar"></span>
                <span class="icon-bar"></span>
                <span class="icon-bar"></span>
            </button>
            <a class="navbar-brand" href="#">Bucket Study</a>
        </div>
        <div class="navbar-collapse collapse">
            <ul class="nav navbar-nav">
                <li class="active"><a href="#">Home</a></li>
            </ul>
        </div><!--/.nav-collapse -->
    </div>
</div>

<div class="container">
    <%= yield %>
</div>
</body>
```

## Devise?
https://github.com/ttochi/web/blob/master/Rails/%5BGEM%5D%20Devise.md

User 모델은 `nickname`과 `profile_img` 속성을 추가로 갖고있다.

## Message Model 생성
유저들이 주고받을 메세지 모델을 생성하자
```
rails g model Message content:string
```
--> `Message` 데이터에 대한 model 파일과 migration 파일이 생성

Message 모델에 대한 controller를 생성하자

## 모델 간 관계 설정
`user`와 `message`는 1:N 관계를 가질 때 `model` 파일에는 다음과 같이 관계를 선언해준다.

### 모델 파일 수정
`models/user.rb`:
```ruby
class User < ActiveRecord::Base
    has_many :messages, dependent: :destroy
end
```
> `has_many`는 복수형으로 선언해줘야 한다!<br>
> `dependent: :destroy` 옵션으로 `user`를 destroy할 때 해당 유저에 속하는 모든 `message`가 함께 삭제된다

`models/message.rb`:
```ruby
class Message < ActiveRecord::Base
    belongs_to :user
end
```
> `belongs_to`는 단수형

### migration 파일 수정
관계 선언만으로 DB 테이블이 자동으로 연결되지 않음! 꼭 foregin key 설정해야 함<br>
rails에서는 기본적으로 모든 테이블의 primary key가 id값이다

`messages` table에 `user_id` 필드를 추가하자.
```
rails g migration add_user_id_to_messages user_id:integer:index
```

1. `add_user_id_to_messages` 라는 migration file을 생성
2. 필드명과 데이터명을 이어서 생성 `user_id:integer`
3. `index` 옵션을 지정하여 DB에서 빠른 검색을 가능하게 함

이렇게 생성된 migration file에 대해 db:migrate 작업을 수행

### M:N 관계 선언은?
http://rubykr.github.io/rails_guides/association_basics.html

1. `has_mnay :through`
2. `has_and_belongs_to_many`

## Form
Rails에서 입력 양식 `<form> 태그`와 관련한 view helper는 크게 2가지 종류로 구분된다.

1. **form_tag** : 범용적인 입력 양식 생성
2. **form_for** : 특정 모델에 특화된 입력 양식 생성

`form_tag` 메소드는 검색어를 받는 경우와 같이 모델과 관련되지 않는 경우에 주로 쓰인다.<br>
`form_for` 메서드에서는 form 양식으로 모델 객체 `f.`으로 호출하는 `f.*` 헬퍼를 사용한다.

### form_for 메소드
`form_for` 메소드는 특정 모델의 생성 및 편집 작업을 할 때 쓰인다.
```ruby
form_for [var] [option] do |f|
    ...
end
```

> var는 form에서 사용되는 모델 객체를 의미한다.
> option에는 아래와 같은 것들이 들어간다.
> 
> * url: 입력 양식 전송 대상 링크
> * html: 이외의 모든 form 태그 속성 정보 (method, multipart, id/class)

### f.* 헬퍼
`form_for` 메서드에서는 form 양식으로 모델 객체 `f.`으로 호출하는 `f.*` 헬퍼를 사용한다.
```ruby
f.text_field [prop] [option]
```

> prop은 모델 객체가 갖는 속성값이다.
> option은 각 form field tag가 갖는 옵션값이다.

### 레코드 식별 의존성

`form_for`를 쓰면 사용되는 모델 객체의 상태에 따라 적절한 url, html 옵션이 자동으로 붙여진다.

예를 들어, 위의 예제에서 `@expert_user`가 아래와 같이 `new` 액션에서 정의되어 오면
```ruby
@expert_user = ExpertUser.new
```

다음과 같이 옵션이 자동으로 지정되어 `create` 액션이 실행된다.
```RHTML
<%= form_for @expert_user, url: expert_users_path, html: {id: :new_expert_user, class: :new_expert_user} do |f| %>
```

`@expert_user`가 `edit` 액션에서 정의되어 온다면
```ruby
@expert_user = ExpertUser.find(param[:id])
```

다음과 같이 옵션이 자동으로 지정되어 `update` 액션이 실행된다.
```RHTML
<%= form_for @expert_user, url: expert_users_path(@expert_user), html: {id: :edit_expert_user, class: :edit_expert_user_#, method: :patch} do |f| %>
```

### Message를 생성하는 form을 작성합시다

#### 0. css
```scss
#contents {
  > .message {
    overflow: hidden;
    margin-bottom: 20px;

    > .profile {
      margin-right: 20px;
      float: left;
      width: 35px;
      height: 35px;
    }

    > .nick {
      font-size: 14px;
      font-weight: bold;
      color: navy;
      line-height: 15px;
    }

    > .content {
      float: left;
      margin-top: 5px;
      font-size: 14px;
      line-height: 15px;
    }

    > .time {
      float: right;
      font-size: 11px;
      line-height: 15px;
      color: sandybrown;
    }
  }
}
```

#### 1. message controller 생성

new, create action 생성
```ruby
class MessagesController < ApplicationController
    before_action :authenticate_user!, only: [:new]

    def new
        @message = Message.new
    end

    def create
        @message = Message.new(messages_params)

        respond_to do |format|
            if @message.save
                format.html { redirect_to :back, notice: '메세지 생성되었습니다.' }
            else
                format.html { render action: 'new' }
            end
        end
    end

    private
    def messages_params
        params.require(:message).permit(:content, :user_id)
    end
end
```

#### 2. router 설정

```
resources :messages, only: [:create, :new]
```

#### 3. new view 생성

```rhtml
<%= form_for @message do |f| %>
    <%= f.hidden_field :user_id, value: current_user.id %>
    <%= f.text_field :content, placeholder: '채팅을 하자!' %>
    <%= f.submit :submit, value: '확인', id: 'submit' %>
<% end %>
```

#### 4. 메세지를 보여주자

```rhtml
<% @messages.each do |m| %>
    <div class="message">
        <%= image_tag m.user.profile_image_url, class: 'profile' %>
        <div class="nick"><%= m.user.nickname %></div>
        <div class="content"><%= m.content %></div>
        <div class="time"><%= m.created_at %></div>
    </div>
<% end %>
```

### ajax로 새로고침 없이 채팅정보를 불러보자

#### 1. javascript
`layout/application.html.erb`:
```rhtml
<%= yield(:custom_js) %>
```

```rhtml
<% content_for :custom_js do %>
    <%= javascript_include_tag 'message' %>
<% end %>
```

ajax in js
```javascript
$.ajax({
    url: //,
    success: function (data) {
        //
    }, dataType: '//'
});
```

어떤 data를 렌더링 할거냐?
