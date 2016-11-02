# Socket Study 1
Rails 개발을 위한 로컬 환경 설정부터 웹서버 구축까지 진행합니다

## 개발 환경 준비

### 1. rbenv를 통한 ruby 설치
`rbenv`와 `rvm`은 루비버전관리자입니다
```bash
brew update
brew install rbenv ruby-build
```

ruby 설치
```bash
rbenv install 2.2.1
```

rails 설치
```
gem install rails -v '4.2.6' -V --no-ri --no-rdoc
```

### 2. 에디터

## 레일즈의 동작 원리

### MVC 모델
MVC 모델이란, 하나의 웹 애플리케이션(web application)을 모델(model), 뷰(view), 컨트롤러(controller)라는 세 가지 요소로 나누어 각각에 대하여 고유한 역할을 부여하는 일종의 설계 패턴이다. 

**1. 모델(model)**

모델(model)은 데이터를 다루는 요소이다. 모델은 DBMS(MySQL, MongoDB, SQLite 등)를 통해 데이터베이스(database) 상에서 새로운 데이터베이스 테이블(table)을 만들고, 새로운 데이터를 테이블에 저장하고, 기존에 저장된 데이터를 읽어들인 뒤 수정하는 등 데이터를 다루는 거의 모든 작업을 담당한다. 레일스에서는 Active Record라는 장치가 곧 모델의 실체가 되며, Active Record의 사용자 친화적인 명령어들을 사용하여 DBMS를 편리하게 조작할 수 있다.

**2. 뷰(view)**

뷰(view)는 웹 서비스 사용자들에게 직접적으로 보여지는 부분을 다루는 요소이다. 클라이언트가 어떤 웹 페이지를 요청했을 시, 이들에게 보여지는 웹 페이지의 외관을 구성하는 요소들을 아우른다. 즉, 클라이언트 측 프로그래밍 기술인 HTML, CSS, JavaScript 등의 파일들을 모두 모아놓은 것이라고 생각하면 된다.

**3. 컨트롤러(controller)**

컨트롤러(controller)는 모델과 뷰 중간에서 둘 간의 상호작용을 중재하는 요소이다. 컨트롤러는 클라이언트의 URL 요청을 받아 웹 페이지를 보여주고자 하는데, 그 과정에서 필요한 데이터를 얻기 위해 모델에게 데이터를 요청하고, 모델이 DBMS를 조작하여 해당 데이터를 찾아와 컨트롤러로 전달해주면, 컨트롤러는 이 데이터를 해당 웹 페이지의 뷰에 반영한 뒤 클라이언트 측으로 제공하는 것이다.

### router
클라이언트는 서버 측으로 요청을 보낼 때 URL 형태로 보내는데, 서버에서 해당 URL과 관련된 컨트롤러의 메서드로 매칭하는 작업을 ‘라우트(route)를 지정한다’고 하며, ‘라우팅(routing)’이라는 단어로도 표현한다.

## 프로젝트

### 1. 프로젝트 생성
```bash
rails new <project_name>
```

### 2. Gem?
Gem이란 루비의 일종의 라이브러리
bundler를 통해 gemfile을 관리
```bash
gem install bundler
```

gemfile에 명시된 gem들을 사용할 수 있다.
```bash
bundle install
```

### 3. Webrick 실행
webrick은 루비에서 제공하는 간단한 웹서버 서비스
프로젝트 파일에서 WEBrick 실행
```bash
rails server
```

`http://localhost:3000`에 접속!

1. browser에 request 전송
2. router에서 url 매칭 후 해당 controller에 request 전송
3. controller에서 해당 요청 수행
4. 요청 결과를 view에 전송
5. view에서 HTML 형태로 render해서 컨트롤러에 돌려줌
6. 컨트롤러에서 브라우저로 결과를 뿌려줌

### 4. controller 작성
```bash
rails generate controller Home
```

`index`라는 액션을 만들어보자

router에 해당 컨트롤러#액션을 취하는 라우트 정보 생성
```ruby
get 'welcome' => 'home#index'
```

### 5. View 생성
`app/views/home/index.html.erb`를 생성하여 간단한 html 파일을 작성해보자

`http://localhost:3000/index`에 접속!
home 컨트롤러의 index 액션에 요청을 전송 --> app/views/welcome/index.html.erb 파일이 렌더링됨


### 6. Model 생성
간단한 모델 하나를 생성하자
```bash
rails generate model Message
```

--> `Message` 데이터에 대한 model 파일과 migration 파일이 생성

migration을 보자!
migration은 db의 구조를 생성 수정 작업하는 파일
`db/migrate` 폴더 안에 형성된 `201606260000_create_모델명복수형.rb`

```bash
rake db:migrate
```

이제 `Message`라는 controller에 `index` action을 생성해보자
```ruby
def index 
  @messages = Message.all 
end
```

### 7. scaffold로 생성
우선은 바로 서버구축 작업에 들어가기 위해 스캐폴드로 모델을 생성한다.
스캐폴드를 이용하면 CRUD에 대한 액션과 뷰가 바로 생성됨.

