# Basic Form Helper

## 1. radio 태그

```RHTML
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

## 2. check box 태그

```RHTML
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

## 3. Select Box
Select boxes in HTML require a significant amount of markup (one OPTION element for each option to choose from), therefore it makes the most sense for them to be dynamically generated. Let's see how Rails can help out here.

### 3.1 select_tag

```RHTML
<%= select_tag(:city_id, '<option value="1">Lisbon</option>...') %>
```

option 태그 부분을 string으로 써야함 -> `option_for_select` helper를 이용하면 option_tag 생성이 가능
```RHTML
<%= select_tag(:city_id, options_for_select(...)) %>
```

### 3.2 options_for_select

```RHTML
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

```RHTML
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

### 3.3 f.select

```RHTML
<%= f.select :place, options_for_select(array_to_select_options(Constants::Place::STR), f.object[:place]), {include_blank: '공간 선택(필수)'}, {required: true, class: 'select_place'} %>
<%= f.select :style, options_for_select(array_to_select_options(Constants::Style::STR), f.object[:style]), {include_blank: '스타일 선택'}, {class: 'select_style'} %>
```

### 3.4 option tags for model object collection
`options_for_select` 를 쓰려면 각 option마다 text와 value로 이뤄진 array가 필요하다. 하지만 select option이 model로서 존재하고 그 객체에 대한 option tag를 만들고 싶은 경우, iterating을 통해 nested array를 만들어서 해결할 수 있다.
```RHTML
<% cities_array = City.all.map { |city| [city.name, city.id] } %>
<%= options_for_select(cities_array) %>
```

또는 `options_from_collection_for_select` 헬퍼로 이러한 `option tag`를 쉽게 만들 수 있다.
```RHTML
<%= collection_select(:person, :city_id, City.all, :id, :name) %>
```

> two addtional argument: option **value** and **text**

`option tag` 뿐 아니라 `select_tag`와 함께 사용하기 위해 `collection_select` 헬퍼를 사용한다.
```RHTML
<%= f.collection_select(:city_id, City.all, :id, :name) %>
```

## 4. 그 외의 태그

```RHTML
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


<br><br><br>
[RailsGuideFormHelper](http://guides.rubyonrails.org/form_helpers.html#helpers-for-generating-form-elements) 참고