# Form Method

Rails에서 입력 양식 `<form> 태그`와 관련한 view helper는 크게 2가지 종류로 구분된다.

1. **form_tag** : 범용적인 입력 양식 생성
2. **form_for** : 특정 모델에 특화된 입력 양식 생성

## 1. 일반적인 폼을 위한 form_tag

### 1.1 form_tag 메소드
`form_tag` 메소드는 검색어를 받는 경우와 같이 모델과 관련되지 않는 경우에 주로 쓰인다.
```RHTML
form_tag [url] [option] do
    ...
end
```

> 1. url: 입력 양식 전송 대상 링크이다.
> 2. option: 아래의 옵션을 설정한다.
    * method: form 태그에서 사용하는 http 메서드 (기본적으로 post)
    * multipart: 파일을 업로드할 때 사용
    * id / class

### 1.2 *_tag 헬퍼
`form_tag` 메소드에서는 form 양식으로 끝에 `_tag`가 붙는 `*_tag` 헬퍼를 사용한다.
```RHTML
text_field_tag [name] [value] [option]
```

> 1. name: form을 보낼 때의 입력 변수 이름이다.
> 2. value: 속성 값으로 비어있는 경우에도 꼭 표시를 해준다.
> 3. option: 각 form field tag가 갖는 옵션값이다.

**ex) navigation의 search form**
```RHTML
<%= form_tag 'url', method: 'get/post', id: 'id_name' do %>
    <%= text_field_tag :query, 'value' %>
    <%= hidden_field_tag :type, 'value' %>
<% end %>
```

## 2. 모델 객체를 위한 form_for

특정 모델을 creating or editing할 때 `*_tag` 헬퍼를 사용하면 매번 모델을 수정하기 위해 사용되는 parameter 이름을 신경써야하는 불편함이 생긴다.
```RHTML
<%= text_field(:person, :name) %>
```

output:
```html
<input id="person_name" name="person[name]" type="text" value="Henry"/>

//value는 params[:person][:name]에 저장되있던 값
```

이와 같이 `_tag` suffix를 없애고 수정할 모델을 함께 선언해주면 이를 해결할 수 있다.
이 때 같은 모델에 대해서 한번에 수정하기 위해 `form_for` 메소드를 사용한다.

### 2.1 form_for 메소드
`form_for` 메소드는 특정 모델의 생성 및 편집 작업을 할 때 쓰인다.
```RHTML
form_for [var] [option] do |f|
    ...
end
```

> var는 form에서 사용되는 모델 객체를 의미한다.
> option에는 아래와 같은 것들이 들어간다.
> 
> * url: 입력 양식 전송 대
> * html: 이외의 모든 form 태그 속성 정보 (method, multipart, id/class)

### 2.2 f.* 헬퍼
`form_for` 메서드에서는 form 양식으로 모델 객체 `f.`으로 호출하는 `f.*` 헬퍼를 사용한다.
```RHTML
f.text_field [prop] [option]
```

> prop은 모델 객체가 갖는 속성값이다.
> option은 각 form field tag가 갖는 옵션값이다.

**ex) 전문가/판매자 신청, 수정 페이지**
```RHTML
<%= form_for @expert_user, html: {id: :seller_form} do |f| %>
    <%= f.hidden_field :division, value: 0 %>
    <%= f.text_field :company, required: true %>
    <%= radio_button_tag :type, 0, ('checked=checked' if @expert_user.expert_type != 1) %>
    <%= radio_button_tag :type, 1, ('checked=checked' if @expert_user.expert_type == 1) %>
    <%= f.submit :submit, value: '전문가 신청 완료', id: 'seller_submit' %>
<% end %>
```

### 2.3 레코드 식별 의존성

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
