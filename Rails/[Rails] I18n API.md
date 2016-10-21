# I18n API
프로젝트의 문장들의 여러 언어를 지원하는 기능을 간단하고 확장 가능한 방식으로 도입할 수 있는 프레임워크

Rails는 미리 지정된 `locale 언어`를 사용해 `사전 파일`에서 언어를 선택하여 해당 내용을 `view`에 출력한다.

## 1. Locale 설정
locale 언어 정보는 설정파일 `application.rb`와 `application controller`에서 설정하는 것이 기본이다.

config/application.rb:
```ruby
config.i18n.default_locale = :ko 
```

### 1.1 브라우저 설정에 따른 locale 변경
클라이언트의 브라우저 언어 설정(`Accept-Language 헤더`)에 따라 프로젝트 locale을 변경할 수 있다.

application_controller.rb:
```ruby
class ApplicationController < ActionController::Base
    before_action :detect_locale

    ...중략...

    private
    def detect_locale
        I18n.locale = request.headers['Accept-Language'].scan(/\A[a-z]{2}/).first
    end
end
```

locale을 동적으로 변경하고 싶으면 위의 코드의 `detect_locale` 부분을 잘 설정하면 된다.

### 1.2 url 파라미터로 locale 변경
이번에는 `~/groups/1/?locale=ko`와 같이 locale을 url 파라미터로 넘기도록 설정해보자.

application_controller.rb:
```ruby
class ApplicationController < ActionController::Base
    before_action :detect_locale

    def default_url_options(options = {})
        { locale: I18n.locale }
    end

    private
    def detect_locale
        I18n.locale = params[:locale]
    end
end
```

`default_url_options` 함수를 오버라이딩해서 모든 url에 locale 옵션을 자동으로 붙여준다.

### 1.3 route 변수로 locale 변경
모든 url에 parameter로 locale 정보를 넣는 것이 거슬릴 수 있다.<br>
이때 `~/ko/groups/1`와 같이 url 자체에 locale을 지정할 수 있다.

route에서 `scope`를 활용하면 url 앞부분에 생략가능한 파라미터를 넣을 수 있다.

routes.rb:
```ruby
Rails.application.routes.draw do
    scope '(:locale)', locale: /ko|en/ do
        root 'groups#index'
        resources :groups
        resources :items
    end
end
```

이 때 `/(:locale)/groups/1` 과 같은 형식의 URL을 사용할 수 있게 되며 locale 해당하는 부분은 `params[:locale]`을 참조하게 된다.

> scope에서 정의한 파라미터는 `()`로 쌓이면 생략이 가능하다.

application_controller.rb:
```
1.2와 동일하게 하자!
```

### 1.4 팬메이드의 locale 설정
팬메이드는 default locale로 `:ko`를 지정하였다.<br>
locale parameter 정보가 없으면 기본적으로 브라우저 언어를 따르며<br>
브라우저 언어가 존재하지만 우리가 지원하는 언어(:ko/:en/:ja/:zh-CN)에 포함되지 않으면 default locale을 따른다.<br>
다음으로 사용자가 메뉴바를 통해 설정한 locale을 최우선으로 따른다.

application_controller.rb:
```ruby
class ApplicationController < ActionController::Base
    before_action :detect_locale

    def default_url_options(options = {})
        { locale: I18n.locale }
    end

    private
    def detect_locale
        if params[:locale].blank?
            browser_locale = request.headers['Accept-Language'].scan(/\A[a-z]{2}/).first
            if I18n.locale_available?(browser_locale)
                I18n.locale = browser_locale
            else
                I18n.locale = I18n.default_locale
            end
        else
            I18n.locale = params[:locale]
        end
    end
end
```

route.rb:
```
1.3과 동일하게 하였다
```

## 2. 사전 파일
사전 파일이란 언어 의존적인 컨텐츠를 모아놓은 파일이다.

#### 사전 파일 작성
사전 파일은 `config/locales` 폴더 하에 YAML 형식으로 작성하는 것이 기본이다.

config/locales/ko.yml:
```YAML
ko:
  goods:
    made_by: '#{name}님의 굿즈'
```

이와 같이 들여쓰기를 통해 계층 구조를 만들어서 관리한다.<br>
`goods.made_by`와 같이 키를 사용할 수 있으며 `#{name}`과 같이 값을 동적을 변경할 수 있는 placeholder를 설정할 수 있다.

#### View 파일 작성
액션 뷰에서 사전 파일을 참조할 때는 뷰 헬퍼 `t`를 사용한다.
```rhtml
<%= t ‘goods.made_by’, name: @user.name %>
```

## 3. 사전 파일의 구조화

### 3.1 템플릿 단위에 따른 구조화
사전파일은 일반적으로 템플릿 단위로 계층을 설정한다.<br>
즉, `<컨트롤러 이름>.<액션 이름>.<키 이름>`으로 설정하는 것이 일반적이다.

#### 사전 파일 작성
`items#show`에서 사용하는 키는 아래와 같이 사용한다.

```YAML
ko:
  items:
    show: 
        made_by: '#{name}님의 굿즈'
```

#### View 파일 작성
구조 단위로 지정한 키는 해당 액션 템플릿에서 간략하게 사용할 수 있다.

items/show.html.erb:
```rhtml
<%= t ‘.made_by’ %>
```

이 key는 `items.show.made_by` 를 검색한다.

### 3.2 사전 파일 분리
모든 컨텐츠를 하나의 사전 파일의 몰아넣으면 관리가 어려워지므로 컨트롤러 단위로 폴더를 분류해서 사전 파일을 관리하자.
```bash
/locales
    > ko.yml    #공통 한국어 사전
    > en.yml    #공통 영어 사전
    > /views
        > /items    #items controller의 사전
            > ko.yml
            > en.yml
```

이렇게 사전파일을 하위폴더로 분할한다면 어플리케이션 설정 파일도 바꿔줘야 한다.

application.rb:
```ruby
config.i18n.load_path += Dir[Rails.root.join('config', 'locales', '**', '*.{rb,yml}').to_s]
```

사전 파일 검색 폴더를 `/config/locales/**/*.rb, *.yml`로 지정한 것이다.

### 3.3 템플릿 자체로 locale 구분
'이용약관', '개인정보취급방침'과 같이 번역해야 할 내용이 통으로 되어 있을 때, 각 문장을 사전파일로 엮는 것 보다 파일 자체를 지역별로 만드는 것이 더 편리할 것이다.

템플릿 자체로 locale을 구분할 때는 `index.html.erb`같은 형식의 템플릿 파일과 같은 폴더에 `index.ko.html.erb`라는 이름으로 파일을 위치시키면 된다.

이렇게 하면 언어 설정이 `:ko`일 때 `index.ko.html.erb`를 자동으로 호출한다.

하지만 템플릿 파일의 지역화는 디자인을 변경하는 경우 모든 파일을 바꿔줘야 하므로 꼭 사용해야겠다고 판단되는 경우에만 사용하자.

<br><br><br>
퍼펙트 루비 온 레일즈 참고