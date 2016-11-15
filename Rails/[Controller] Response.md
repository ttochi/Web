# [Controller] Response
Rails의 Controller는 요청을 다루는 프로세스의 전반을 관리한다.<br>
Controller는 요청을 해석하고 Model을 호출하여 모든 처리를 끝내면 요청 결과를 View에 전송한다.

이 때 Controller에서 View로 요청 결과를 넘기는 방법에 대해 알아보겠다!

## 1. Creating Response

### 1.1 HTTP 프로토콜이란
HTTP는 클라이언트와 서버 사이에 이뤄지는 request/response 프로토콜이다.<br>
클라이언트(웹브라우저)가 HTTP를 통해 서버로부터 정보를 요청하면 서버는 이에 응답하여 정보를 전달한다.

### 1.2 HTTP Request - GET vs POST
Get은 URL에 해당하는 데이터를 서버에 요청한다.<br>
Post는 서버가 처리할 수 있는 파라미터를 보낸다.

### 1.3 HTTP Response
Controller가 HTTP request를 받고 응답을 생성하는데 크게 3가지 방법이 있다.

**render**
브라우저에 다시보내는 full response를 생성<br>
template, script 등 범용적인 출력

**redirect_to**
브라우저에 HTTP redirect status code를 보낸다<br>
지정한 주소로 처리를 redirect<br>
액션을 거쳐서 감

## 2. render 메서드
`render`는 response 메소드 중에서도 가장 기본적인 메소드이다.<br>
`render`에서 자주 사용되는 기본적인 옵션들에 대해서만 설명하겠다.

> 다른 것까지 알고 싶으면 아래의 참고 문서를 참조하자!

**템플릿 호출**

1) 기본 호출

액션에서 명시적으로 response 메소드를 호출하지 않으면 자동으로 render 메소드가 호출되고
```
app/views/ --> 컨트롤러이름 디렉토리 --> 액션이름 erb 파일
```

위 디렉토리의 erb 파일을 view template 으로 사용하여 응답으로 보낼 html 파일을 렌더링한다.

2) 액션 이름과 다른 템플릿 호출
```ruby
render action: 'index'
render 'index'
```

3) 다른 폴더의 템플릿 호출
```ruby
render template: 'project/index'
render 'project/index'
```

**render 옵션**

1) layout

Rails는 레이아웃을 탐색하는 경우, 우선 현재 컨트롤러와 같은 이름을 가지는 레이아웃이 `app/views/layouts` 에 있는지를 확인한다. 

예를 들어, PhotosController 클래스의 액션을 랜더링하는 경우라고 가정한다면 `app/views/layouts/photos.html.erb`을 우선적으로 찾는다.<br>
해당하는 레이아웃이 존재하지 않는 경우, `app/views/layouts/application.html.erb`를 사용한다.

`:layout` 옵션을 사용하면 현재의 액션에서 특정 파일을 레이아웃으로 사용할 수 있다.
```ruby
render layout: "special_layout"
render layout: false        # layout을 사용하지 않겠다
```

## 3. redirect_to 메서드
`redirect_to`는 지정된 페이지로 처리를 이동하게 한다.
```ruby
redirect_to 'https://ohou.se'   # 절대 경로
redirect_to action :index       # 같은 컨트롤러의 액션
redirect_to controller :project, action :index   # 다른 컨트롤러의 액션
redirect_to projects_path       # path 경로
redirect_to :back               # 이전 페이지
```

### 3.1 render vs redirect_to
EX: render
```ruby
def index
    @books = Book.all
end

def show
    @book = Book.find_by(params[:id])
    if @book.nil?
        render action: "index"
    end
end
```

`render`를 쓰게 되면 `index` action의 코드를 수행하지 않는다. 템플릿만을 호출!<br>
즉, @book은 비어있는 상태에서 `index` action을 부르는 것

EX: redirect_to
```ruby
def index
    @books = Book.all
end

def show
    @book = Book.find_by(params[:id])
    if @book.nil?
        redirect_to action: :index
    end
end
```

반면 `redirect_to`는 바로 새로운 url을 부름.<br>
`redirect_to`의 단점은 브라우저에 show action에 대한 응답을 한 후 `index` action으로 리다이렉트 한다는 점. --> 시간 소요

## 4. respond_to 메서드
`respond_to`는 하나의 action을 이용하여 다양한 포멧 형태로 결과를 받는 방법이다.

웹을 이용하는 클라이언트와 앱을 이용하는 클라이언트를 모두 고려하는 상황에서,<br>
웹브라우져의 html request는 html 형태로 응답해주고<br>
앱프로그램의 json request는 json 형태로 응답해준다.

Ex) 좋아요 반영해주는 praises_controller의 create 액션
```ruby
format.json { render json: {success: true, like_count: @content.praise_count} }
format.js
format.html { render nothing: true }
```

이제 해당 액션은 요청의 종류에 따라 `format.json`, `format.js` 형태를 render할 수 있다.

위 코드의 format.json과 html은 뒤에 render 뒤에 불려진 것들을 간단하게 부르고 format.js는 뒤에 아무것도 없으니 해당 액션 이름의 js 파일을 부름 --> `view/praises/create.js`


<br><br><br>
[Rails 가이드](http://guides.rorlab.org/layouts_and_rendering.html)
퍼펙트 루비 온 레일즈