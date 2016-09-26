# Form Method

[TOC]

Rails에서 입력 양식 `<form> 태그`와 관련한 view helper는 2가지 종류로 구분된다.

1. **form_tag** : 범용적인 입력 양식 생성
2. **form_for** : 특정 모델에 특화된 입력 양식 생성

## 1. 일반적인 폼을 위한 form_tag

### 1.1 form_tag 메서드
`form_tag` 메서드는 검색어를 받는 경우와 같이 모델과 관련되지 않는 경우에 주로 쓰인다.
```ruby
form_tag [url] [option] do
    ...
end
```

> 1. url: 입력 양식 전송 대상 링크이다.
> 2. option: 아래의 옵션을 설정한다.
    * method: form 태그에서 사용하는 http 메서드 (기본적으로 post)
    * multipart: 파일을 업로드할 때 사용
    * id / class

### 1.2 field_tag 헬퍼
`form_tag` 메서드에서는 form 양식으로 끝에 `_tag`가 붙는 `field_tag` 헬퍼를 사용한다.
```ruby
text_field_tag [name] [value] [option]
```

> 1. name: form을 보낼 때의 입력 변수 이름이다.
> 2. value: 속성 값으로 비어있는 경우에도 꼭 표시를 해준다.
> 3. option: 각 form field tag가 갖는 옵션값이다.

**ex) navigation의 search form**
```ruby
<%= form_tag 'url', method: 'get/post', id: 'id_name' do %>
    <%= text_field_tag :query, 'value' %>
    <%= hidden_field_tag :type, 'value' %>
<% end %>
```

## 2. 모델 객체를 위한 form_for
A particularly common task for a form is editing or creating a model object.
While the `*_tag` helpers can certainly be used for this task they are somewhat verbose as for each tag you would have to ensure the correct parameter name is used and set the default value of the input appropriately. Rails provides helpers tailored to this task. These helpers lack the `_tag` suffix.
```ruby
<%= text_field(:person, :name) %>
```
output:
```html
<input id="person_name" name="person[name]" type="text" value="Henry"/>
```

> value는 params[:person][:name]에 저장되있던 값


### 2.1 form_for 메서드
`form_for` 메서드는 특정 모델의 생성 및 편집 작업을 할 때 쓰인다.
```ruby
form_for [var] [option] do |f|
    ...
end
```

> var는 form에서 사용되는 모델 객체를 의미한다.
> option에는 아래와 같은 것들이 들어간다.
> 
> * url: 입력 양식 전송 대
> * html: 이외의 모든 form 태그 속성 정보 (method, multipart, id/class)

### 2.2 f.field 헬퍼
`form_for` 메서드에서는 form 양식으로 모델 객체 `f.`으로 호출하는 `f.field` 헬퍼를 사용한다.
```ruby
f.text_field [prop] [option]
```

> prop은 모델 객체가 갖는 속성값이다.
> option은 각 form field tag가 갖는 옵션값이다.

**ex) 전문가/판매자 신청, 수정 페이지**
```ruby
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

예를 들어, 위의 예제에서 `@expert_user`가 아래와 같이 `new` 컨트롤러에서 정의되어 오면
```ruby
@expert_user = ExpertUser.new
```

다음과 같이 옵션이 자동으로 지정되고,
```ruby
<%= form_for @expert_user, url: expert_users_path, html: {id: :new_expert_user, class: :new_expert_user} do |f| %>
```

`@expert_user`가 `edit` 컨트롤러에서 정의되어 온다면
```ruby
@expert_user = ExpertUser.find(param[:id])
```

다음과 같이 옵션이 자동으로 지정된다.
```ruby
<%= form_for @expert_user, url: expert_users_path(@expert_user), html: {id: :edit_expert_user, class: :edit_expert_user_#, method: :patch} do |f| %>
```

fields_for에 의해서 생성되는 객체는 폼 빌더이며, form_for에서 생성되는 것과 비슷합니다(사실 form_for의 내부에서는 fields_for를 호출합니다).

## 3. 폼에서의 parameter 전달
The first parameter to 'form_tag' is always the name of the input. 
When the form is submitted, the name will be passed along with the form data, and will make its way to the params in the controller with the value entered by the user for that field. 

For example, if the form contains <%= text_field_tag(:query) %>, then you would be able to get the value of this field in the controller with params[:query].

When naming inputs, Rails uses certain conventions that make it possible to submit parameters with non-scalar values such as arrays or hashes, which will also be accessible in params. You can read more about them in chapter 7 of this guide.

```ruby
<%= form_for @article, url: {action: "create"}, html: {class: "nifty_form"} do |f| %>
  <%= f.text_field :title %>
  <%= f.text_area :body, size: "60x12" %>
  <%= f.submit "Create" %>
<% end %>
```

output:
```html
<form accept-charset="UTF-8" action="/articles" method="post" class="nifty_form">
  <input id="article_title" name="article[title]" type="text" />
  <textarea id="article_body" name="article[body]" cols="60" rows="12"></textarea>
  <input name="commit" type="submit" value="Create" />
</form>
```

The name passed to form_for controls the key used in params to access the form's values. Here the name is article and so all the inputs have names of the form article[attribute_name]. Accordingly, in the create action params[:article] will be a hash with keys :title and :body. 

## 4. form field 헬퍼

### 4.2 radio 태그

```ruby
<%= radio_button_tag(:age, "child") %>
<%= label_tag(:age_child, "I am younger than 21") %>
<%= radio_button_tag(:age, "adult") %>
<%= label_tag(:age_adult, "I'm over 21") %>
```

This generates the following:
```html
<input id="age_child" name="age" type="radio" value="child" />
<label for="age_child">I am younger than 21</label>
<input id="age_adult" name="age" type="radio" value="adult" />
<label for="age_adult">I'm over 21</label>
```

Because these two radio buttons share the same name (age), the user will only be able to select one of them, and params[:age] will contain either "child" or "adult".

### 4.3 check box 태그

```ruby
<%= check_box_tag(:pet_dog) %>
<%= label_tag(:pet_dog, "I own a dog") %>
<%= check_box_tag(:pet_cat) %>
<%= label_tag(:pet_cat, "I own a cat") %>
```

This generates the following:
```html
<input id="pet_dog" name="pet_dog" type="checkbox" value="1" />
<label for="pet_dog">I own a dog</label>
<input id="pet_cat" name="pet_cat" type="checkbox" value="1" />
<label for="pet_cat">I own a cat</label>
```

`check_box_tag`의 first parameter는 name of the input. second parameter는 보통 the value of the input.
This value will be included in the form data when the checkbox is checked.

> Tip! Always use labels for checkbox and radio buttons. They associate text with a specific option and, by expanding the clickable region, make it easier for users to click the inputs.

### 4.4 그 외의 태그

```ruby
<%= text_area_tag(:message, "Hi, nice site", size: "24x6") %>
<%= password_field_tag(:password) %>
<%= hidden_field_tag(:parent_id, "5") %>
<%= search_field(:user, :name) %>
<%= telephone_field(:user, :phone) %>
<%= date_field(:user, :born_on) %>
<%= datetime_local_field(:user, :graduation_day) %>
<%= month_field(:user, :birthday_month) %>
<%= week_field(:user, :birthday_week) %>
<%= url_field(:user, :homepage) %>
<%= email_field(:user, :address) %>
<%= color_field(:user, :favorite_color) %>
<%= time_field(:task, :started_at) %>
<%= number_field(:product, :price, in: 1.0..20.0, step: 0.5) %>
<%= range_field(:product, :discount, in: 1..100) %>
```

This generates the following:
```html
<textarea id="message" name="message" cols="24" rows="6">Hi, nice site</textarea>
<input id="password" name="password" type="password" />
<input id="parent_id" name="parent_id" type="hidden" value="5" />
<input id="user_name" name="user[name]" type="search" />
<input id="user_phone" name="user[phone]" type="tel" />
<input id="user_born_on" name="user[born_on]" type="date" />
<input id="user_graduation_day" name="user[graduation_day]" type="datetime-local" />
<input id="user_birthday_month" name="user[birthday_month]" type="month" />
<input id="user_birthday_week" name="user[birthday_week]" type="week" />
<input id="user_homepage" name="user[homepage]" type="url" />
<input id="user_address" name="user[address]" type="email" />
<input id="user_favorite_color" name="user[favorite_color]" type="color" value="#000000" />
<input id="task_started_at" name="task[started_at]" type="time" />
<input id="product_price" max="20.0" min="1.0" name="product[price]" step="0.5" type="number" />
<input id="product_discount" max="100" min="1" name="product[discount]" type="range" />
```

> 주의! If you're using password input fields (for any purpose), you might want to configure your application to prevent those parameters from being logged. You can learn about this in the [Security Guide](http://guides.rubyonrails.org/security.html#logging).

## 5. 모델 관계에 대한 폼 생성

### 5.1 fields_for
관계를 갖는 다른 모델에 대한 form 동시 처리!

You can create a similar binding without actually creating `<form>` tags with the fields_for helper.

This is useful for editing additional model objects with the same form.

For example, if you had a `Person` model with an associated `ContactDetail` model, you could create a form for creating both like so:

```ruby
<%= form_for @person, url: {action: "create"} do |person_form| %>
  <%= person_form.text_field :name %>
  <%= fields_for @person.contact_detail do |contact_detail_form| %>
    <%= contact_detail_form.text_field :phone_number %>
  <% end %>
<% end %>
```

output:
```html
<form accept-charset="UTF-8" action="/people" class="new_person" id="new_person" method="post">
  <input id="person_name" name="person[name]" type="text" />
  <input id="contact_detail_phone_number" name="contact_detail[phone_number]" type="text" />
</form>
```

### 5.n Validation

## 6. Select Box
Select boxes in HTML require a significant amount of markup (one OPTION element for each option to choose from), therefore it makes the most sense for them to be dynamically generated. Let's see how Rails can help out here.

## 6.1 select_tag

```ruby
<%= select_tag(:city_id, '<option value="1">Lisbon</option>...') %>
```

option 태그 부분을 string으로 써야함 -> `option_for_select` helper를 이용하면 option_tag 생성이 가능
```ruby
<%= select_tag(:city_id, options_for_select(...)) %>
```

## 6.2 options_for_select

```ruby
<%= options_for_select([['Lisbon', 1], ['Madrid', 2], ...], 2) %>
```

output:
```html
<option value="1">Lisbon</option>
<option value="2" selected="selected">Madrid</option>
```

> first: option text와 value로 이뤄진 nested array
> second: pre-select 옵션

hash를 이용하면 추가적인 속성도 넣을 수 있다.

```ruby
<%= options_for_select(
  [
    ['Lisbon', 1, { 'data-size' => '2.8 million' }],
    ['Madrid', 2, { 'data-size' => '3.2 million' }]
  ], 2
) %>
```

output:
```html 
<option value="1" data-size="2.8 million">Lisbon</option>
<option value="2" selected="selected" data-size="3.2 million">Madrid</option>
```

### 6.3 Option Tags from a Collection of Arbitrary Objects
//todo

## 6. 커스텀 폼 만들기