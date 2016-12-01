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
```html
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
</div> <!-- /container -->
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


