#[GEM] Devise

Devise gem은 회원가입/인증/권한설정 등을 구현하기 위한 대표적인 rails gem이다.<br>
[Devise Github](https://github.com/plataformatec/devise)<br>
[Gitbook](https://www.gitbook.com/book/luciuschoi/exploring_devise/details)을 참조하면서 샘플 프로젝트 작성을 진행한다!

## Devise Setup
회원인증 및 권한설정을 위해 `Gemfile`에 아래 젬들을 설치한다.
```ruby
gem 'devise'     # 회원가입 및 인증
```
```bash
$ rails g devise:install
      create  config/initializers/devise.rb
      create  config/locales/devise.en.yml
===============================================================================

Some setup you must do manually if you havent yet:

  1. Ensure you have defined default url options in your environments files. Here
     is an example of default_url_options appropriate for a development environment
     in config/environments/development.rb:

       config.action_mailer.default_url_options = { host: 'localhost', port: 3000 }

     In production, :host should be set to the actual host of your application.

  2. Ensure you have defined root_url to *something* in your config/routes.rb.
     For example:

       root to: "home#index"

  3. Ensure you have flash messages in app/views/layouts/application.html.erb.
     For example:

       <p class="notice"><%= notice %></p>
       <p class="alert"><%= alert %></p>

  4. You can copy Devise views (for customization) to your app by running:

       rails g devise:views

===============================================================================
```
실행 결과에서 보이는 안내를 진행하자

1. devise 젬에 회원가입 안내 메일을 보내기 위한 작업으로 `config/environments` 파일에 action_mailer 설정을 추가한다.

2. 새로운 controller/action을 생성하고 route 파일에 root url을 지정한다.

3. `application.html.erb`에 flash message를 띄우기 위한 코드를 추가한다.

4. `devise`의 뷰 템플릿 파일을 쉽게 복사할 수 있다.

> `config/initializers/devise.rb`에서 devise와 관련된 다양한 정보들을 설정할 수 있다.<br>
> no-reply 메일주소나 비밀번호 최소 길이 등등..

## User Model 생성

devise 젬에서 필요로 하는 User model을 생성한다.
```bash
$ rails g devise User
```
### 1) Model과 migration file이 생성
위의 명령어로 user의 migration 파일과 model 파일이 생성된다.

**본인 확인 메일 관련 설정**
만약 회원등록시 본인 확인 메일을 보내려면 아래와 같은 작업을 진행한다.<br>
우선 `db/migrate/20140528023115_devise_create_users.rb` 파일에서 `## Confirmable` 부분 아래의 네 줄과 하단의 세번째 add_index 부분의 커멘트 처리 부호를 제거하자
```ruby
      ## Confirmable
      t.string   :confirmation_token
      t.datetime :confirmed_at
      t.datetime :confirmation_sent_at
      t.string   :unconfirmed_email # Only if using reconfirmable

      ...중략...

      add_index :users, :confirmation_token,   unique: true
```

그리고 User 모델 클래스 `user.rb`에서 아래에 관련 strategy(:confirmable)를 추가한다.
```ruby
class User < ActiveRecord::Base
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :trackable, :validatable,
         :confirmable
end
```

### 2) route에 devise를 위한 라우트가 생성
```ruby
Rails.application.routes.draw do
  devise_for :users
  root 'welcome#index'
end
```
devise 라우트로 생성되는 path는 아래와 같다!<br>
위의 routes path를 사용하여 회원가입, 회원탈퇴, 로그인, 로그아웃을 쉽게 구현할 수 있다.

```bash
$ rake routes
                  Prefix Verb   URI Pattern                       Controller#Action
        new_user_session GET    /users/sign_in(.:format)          devise/sessions#new
            user_session POST   /users/sign_in(.:format)          devise/sessions#create
    destroy_user_session DELETE /users/sign_out(.:format)         devise/sessions#destroy
           user_password POST   /users/password(.:format)         devise/passwords#create
       new_user_password GET    /users/password/new(.:format)     devise/passwords#new
      edit_user_password GET    /users/password/edit(.:format)    devise/passwords#edit
                         PATCH  /users/password(.:format)         devise/passwords#update
                         PUT    /users/password(.:format)         devise/passwords#update
cancel_user_registration GET    /users/cancel(.:format)           devise/registrations#cancel
       user_registration POST   /users(.:format)                  devise/registrations#create
   new_user_registration GET    /users/sign_up(.:format)          devise/registrations#new
  edit_user_registration GET    /users/edit(.:format)             devise/registrations#edit
                         PATCH  /users(.:format)                  devise/registrations#update
                         PUT    /users(.:format)                  devise/registrations#update
                         DELETE /users(.:format)                  devise/registrations#destroy
       user_confirmation POST   /users/confirmation(.:format)     devise/confirmations#create
   new_user_confirmation GET    /users/confirmation/new(.:format) devise/confirmations#new
                         GET    /users/confirmation(.:format)     devise/confirmations#show
                    root GET    /                                 welcome#index

```

`rake db:migrate`를 실행하고 서버를 재시작한다.<br>

## Devise View 설정
devise에서 제공하는 뷰 템플릿을 사용하려면 `rails g devise:views`를 수행하자<br>
그러면 기본적으로 `view/devise` 디렉토리가 형성되고 모든 devise 관련 뷰는 이곳을 통하게 된다.<br>
만약 devise 모델이 `User`와 `Admin`과 같이 여러 개이고 view를 각각 다르게 해주고 싶다면 다음과 같이 설정해주자<br>

config/initializer/devise.rb:
```ruby
    config.scoped_views = true
```

`sessions/new`를 rendering 하기 전에 `users/sessions/new`를 먼저 체크한다.

## Custom Field 추가하기
User 모델에 `nickname`이라는 column 속성을 추가하고 회원 가입을 할 때 `nickname`값을 같이 받는 작업을 진행하자.

### 1) Migration 파일 수정
migration 파일에 `nickname` 속성을 추가한다

### 2) View 수정
devise의 회원가입 form에 `nickname` field를 추가한다.

### 3) Registration Controller 수정
회원가입 시 `nickname`값도 같이 받기 위해 devise의 controller의 strong parameter값도 customize 해줘야 한다.
```ruby
class RegistrationsController < Devise::RegistrationsController
    private
    def sign_up_params
        params.require(:user).permit(:nickname, :email, :password, :password_confirmation)
    end

    def account_update_params
        params.require(:user).permit(:nickname, :email, :password, :password_confirmation, :current_password)
    end
end
```

`registrations_controller.rb`를 생성하여 기존의 strong parameter를 설정하는 함수를 override 시킨다.

### 4) 라우터 수정
위에서 생성한 컨트롤러로 연결되도록 설정한다.
```ruby
devise_for :users, :controllers => {registrations: 'registrations'}
```

## Devise의 헬퍼메소드
devise는 모든 컨트롤러와 액션에서 사용 가능한 헬퍼메소드를 제공한다.

- before_action :authenticate_user!
- current_user
- user_signed_in?
- user_session

### 1) 리소스에 대한 접근 제한
로그인을 하지 않은 상태에서 특정 controller#action에 대한 접근을 제한하고자 할 때 쓰인다!

scaffold로 생성된 Model에 대해 컨트롤러에서 다음과 같이 설정해주면
```ruby
  before_action :authenticate_user!, only: [ :new, :edit, :create, :update, :destroy ]
```
`index`와 `show` 액션은 로그인 없이 실행할 수 있는 반면 그 외의 CRUD 액션은 로그인을 하지 않으면 접근이 제한된다.

### 2) 현재 사용자 정보
`current_user`는 현재 로그인한 사용자의 데이터를 제공한다.

### 3) 사용자 로그인 여부
`user_signed_in?`을 통해 현재 유저의 로그인 여부를 알려준다.

# [GEM] OmniAuth
devise의 `OmniAuth` gem은 웹어플리케이션에서 다양한 인증 제공서비스 (facebook, kakao 등)을 이용할 수 있게 해주는 라이브러리이다.

다양한 인증 서비스에 쉽게 연결할 수 있도록 업체별 전용 strategy가 젬 형태로 제공되고 있다.<br>
예를 들어, Facebook에 연결을 하고자할 때는 `omniauth-facebook` 젬을, Twitter에 연결하고 싶을 때는 `omniauth-twitter` 젬을 사용하면 되는 것이다.

사용할 수 있는 strategy 목록은 [List of Strategies](https://github.com/omniauth/omniauth/wiki/List-of-Strategies)를 참고하자.

## Facebook 로그인 구현
여기서 수행하는 일련의 과정들은 facebook 뿐 아니라 모든 service provider에서 공통으로 적용된다!

### 1) Gemfile 수정

우선 `Gemfile`에 다음을 추가하고 `bundle install`을 실행한다.
```ruby
gem 'omniauth-facebook'
```
### 2) User 속성 추가

`User` 모델에 페이스북 정보와 관련된 속성을 추가한다.

* **provider** : 인증서비스 제공업체명을 저장할 속성 (facebook, twitter 등)
* **uid** : 사용자 아이디를 저장할 속성
* name : 페이스북 사용자명을 저장할 속성
* image : 페이스북 프로필 사진을 저장할 속성

```bash
$ rails g migration add_columns_to_users provider uid name image
```
### 3) OmniAuth Service Key 추가

다음으로 `config/initializers/devise.rb` 파일에 아래 내용을 추가한다.
```ruby
config.omniauth :facebook, "APP_ID", "APP_SECRET"
```

> 페이스북 `APP_ID` 와 `APP_SECRET` 은 [facebook 개발자 페이지](https://developers.facebook.com) 에서 앱을 생성하면 쉽게 얻을 수 있다.

### 4) User Model 수정

우리가 사용할 service provider를 명시하기 위해 `User` 모델 class에 아래를 추가한다.
```ruby
devise :omniauthable, omniauth_providers: [:facebook]
```

> 이와 같이 추가해주면 `omniauth`용 라우팅이 자동으로 생성된다. `rake routes`를 통해 아래의 path가 새로 추가된 것을 볼 수 있다.
```bash
user_facebook_omniauth_authorize GET|POST /users/auth/facebook(.:format)          devise/omniauth_callbacks#passthru
 user_facebook_omniauth_callback GET|POST /users/auth/facebook/callback(.:format) devise/omniauth_callbacks#facebook
```

또한 `find_for_oauth` method를 추가하여 페이스북 인증이 성공적으로 이루어 졌을 때 information을 추출한다.

```ruby
  def self.find_for_oauth(auth)
      where(provider: auth.provider, uid: auth.uid).first_or_create do |user|
        user.provider = auth.provider
        user.uid = auth.uid
        user.email = auth.info.email
        user.password = Devise.friendly_token[0,20]
        user.name = auth.info.name
        user.image = auth.info.image
      end
  end
```
> method 명은 임의로 정해도 된다.<br>
> 같은 `provider`, `uid`를 가진 회원을 찾고, 없다면 새로 만들어 반환한다.<br>
> 이때 이메일과 이름, 프로필 등의 유저 데이터는 페이스북 정보를 이용하고 비밀번호는 랜덤으로 생성한다.<br>
> name, email, image 같은 정보는 user model에 속성이 존재할 때 사용하면 된다.

### 5) callback URL을 처리할 Controller 추가

페이스북 인증이 성공적으로 이루어 졌을 시 불러질 콜백 메서드를 만들어야 한다!<br>
우선 route 파일에서 `devise_for :users`를 아래와 같이 수정한다.
```ruby
devise_for :users, :controllers => { :omniauth_callbacks => "omniauth_callbacks" }
```

다음으로 controller 폴더에 `omniauth_callbacks_controller.rb`를 생성하고 아래와 같이 작성한다.  
```ruby
class OmniauthCallbacksController < Devise::OmniauthCallbacksController
  def facebook
    @user = User.find_for_facebook_oauth(request.env["omniauth.auth"])

    if @user.persisted?
      sign_in_and_redirect @user, :event => :authentication
      set_flash_message(:notice, :success, :kind => "Facebook") if is_navigational_format?
    else
      session["devise.facebook_data"] = request.env["omniauth.auth"]
      redirect_to new_user_registration_url
    end
  end
end
```
> `request.env["omniauth.auth"]`는 페이스북 정보 데이터이다.<br>
> 제공하는 정보는 서비스마다 다르므로 확인하여 필요한 정보를 뽑아오쟈<br>
> 유저 정보를 방금 User Model에서 정의한 method를 이용해 불러온다.<br>
유저가 유효하면 로그인. 그렇지 않으면 회원가입 폼으로 이동.

## 오늘의 집의 SNS 로그인
오늘의 집에서 페이스북, 카카오톡으로부터 사용자 프로필 이미지 정보를 요구한다.<br>
이메일 정보와 페스워드 정보는 SNS 인증이 성공적으로 이뤄진 후 추가적으로 요구하여 받는다.

`model/user.rb`에서 아래와 같이 provider와 uid가 일치하는 유저 정보를 가져오는 `find_by_oauth` method를 생성한다.

```ruby
def self.find_by_oauth(auth)
    user = where(provider: auth.provider, uid: auth.uid).first
    user
end
```
`controller/omniauth_callback_controller.rb`의 facebook 액션은 다음과 같이 정의된다.

```ruby
def facebook
    @user = User.find_by_oauth(request.env['omniauth.auth'])

    if @user.nil?
        filename = Time.now.to_i.to_s + '_facebook_' + request.env['omniauth.auth'].uid + '.jpg'
        User.facebook_profile_resize(request.env['omniauth.auth'].uid, filename)

        session[:sns_provider] = request.env['omniauth.auth'].provider
        session[:sns_uid] = request.env['omniauth.auth'].uid
        session[:sns_profile] = 'https://d12gkpu9h0k5iq.cloudfront.net/uploads/users/profile_images/' + filename

        respond_to do |format|
            format.html { redirect_to sns_sign_up_normal_users_path }
        end
    else
        sign_in_and_redirect @user, event: :authentication
        set_flash_message(:notice, :success, kind: 'Facebook') if is_navigational_format?

        ...중략...
    end
end
```
`find_by_oauth` method를 콜하여 가져온 유저 정보가 존재한다면 로그인을 시킨다.

유저 정보가 존재하지 않다면 위에서는 `find_by_oauth`에서 새 객체를 생성해주었지만, 추가적으로 email과 password 정보를 가져오기 위해 session에 provider, uid, profile data를 저장하고 `sns_sign_up_normal_users_path` 로 보낸다. -> 추가 정보를 받고 유저 객체 생성

<br>
[참고사이트](https://www.digitalocean.com/community/tutorials/how-to-configure-devise-and-omniauth-for-your-rails-application)