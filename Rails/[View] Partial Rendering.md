# [View] Partial Renderer
`partial template`은 응답으로 넘겨줄 페이지의 특정 부분의 코드를 별도의 파일로 저장한 것이다.

여러 개의 페이지에서 공유하고 싶은 부분을 partial로 만들어 다른 템플릿에서 가져와 출력할 수 있다.

partial을 사용하면 코드의 중복을 막을 수 있으며 view에서 코드의 가독성을 높일 수 있다.

## 1. Using Partial
production 모델의 아이템을 같은 형식으로 `productions#index`와 `orders#show`에서 띄워줘야 하는 상황을 가정해보자:)

### 1.1 Partial Naming
partial의 파일명은 일반적인 템플릿과 구분하기 위해서 `_`로 시작한다.

`productions/_production.html.erb`:
```rhtml
<div class="production" id="product_<%= @production.id %>">
    <%= link_to selling_production_path(@production.id) do %>
        <%= image_tag @production.image_url %>
        <div class="name"><%= @production.name %></div>
        <div class="price"><%= number_to_currency(@production.selling_cost) %></div>
    <% end %>
</div>
```

### 1.2 Partial Render
partial을 view에서 불러오기 위해 render 메서드를 호출해야 한다.
```rhtml
<%= render partial: 'production' %>
<%= render 'production' %>
```

`productions/_production.html.erb`를 호출한다.

## 2. Partial에 parameter 전달
partial에서 partial을 호출한 액션에서 정의한 변수를 그대로 사용할 수 있다. 하지만 템플릿 변수에 의존하는 partial은 범용성 측면에서 문제가 있다.<br>
해당 partial을 이용하는 모든 컨트롤러의 액션 메서드에서 같은 이름을 갖는 템플릿 변수를 만들어줘야 하기 때문이다.

예를 들어 1.1의 코드처럼 `@production` 변수에 의존하는 partial을 사용하려면 해당 partial을 이용하는 모든 컨트롤러의 액션 메서드에서 사용할 템플릿 변수를 모두 `@production`이라는 이름으로 만들어줘야 한다.

따라서 partial에서 사용하는 변수는 가능한 매개 변수로 전달하는 게 좋다.

### 2.1 Partial 수정
1.1의 코드의 템플릿 변수 `@production`을 지역 변수 `production`으로 설정하였다.

`productions/_production.html.erb`:
```rhtml
<div class="production" id="product_<%= production.id %>">
    <%= link_to selling_production_path(production.id) do %>
        <%= image_tag production.image_url %>
        <div class="name"><%= production.name %></div>            
        <div class="price"><%= number_to_currency(production.selling_cost) %></div>
    <% end %>
</div>
```

### 2.2 Partial Render with Parameter
위의 partial을 `order` 컨트롤러에서 부른 `@order_product` 변수로 전달하자

`orders_controller.rb`:
```ruby
def show
    @order = Order.find(params[:id])
    @order_product = Production.where(order_id: @order.id).first
end
```

`orders/show.html.erb`:
```rhtml
<%= render 'productions/production', production: @order_product %>
```

### 2.3 Model에 따른 생략형
Partial 파일이 Model에 대응하면 생략형으로 사용할 수 있다.

위의 코드와 같이 `productions/_production.html.erb`에 **Production 모델**을 전달하는 경우에는 다음과 같이 생략형으로 사용할 수 있다.
```rhtml
<%= render 'productions/production', production: @order_product %>
<%= render @order_product %>
```

**render 메서드는 전달된 모델 `Production`에 대응하는 partial인 `productions/_production.html.erb`를 출력한다.**

### 2.4 여러 매개 변수 전달
view에서 partial로 템플릿 변수 외의 매개 변수를 전달할 수 있다.<br>
`order` 컨트롤러에서 부르는 partial에서는 구매자의 이름을 띄워주는 케이스를 만들어보자.

`productions/_production.html.erb`:
```rhtml
<div class="production" id="product_<%= production.id %>">
    <%= link_to selling_production_path(production.id) do %>
        <%= image_tag production.image_url %>
        <div class="name"><%= production.name %></div>
        <div class="buyer"><%= buyer %></div>
        <div class="price"><%= number_to_currency(production.selling_cost) %></div>
    <% end %>
</div>
```

`orders/show.html.erb`:
```rhtml
<%= render @order_product, locals: { buyer: @order.buyer } %>
<%= render @order_product, buyer: @order.buyer %>
```

## 3. collection 전달
render 메서드에 collection 옵션을 주어 collection(배열)의 각 아이템마다 partial을 렌더링하게 된다.

`orders_controller.rb`:
```ruby
def show
    @order = Order.find(params[:id])
    @order_product = Production.where(order_id: @order.id) # collection
end
```

`orders/show.html.erb`:
```rhtml
<%= render partial: 'productions/production', collection: @order_product, locals: { buyer: @order.buyer } %>
<%= render @order_product, buyer: @order.buyer %>
```

collection 옵션도 Model에 따른 생략이 가능하다

> 주의!
> partial 옵션을 생략할 수 없다.
> parameter는 locals 옵션으로 전달해야 한다.

### 3.1 지역 변수
collection의 각 아이템들은 <U>partial 파일명에 대응되어</U> 자동으로 지역변수 `production`에 할당된다.<br>
collection 아이템의 변수명을 customize하려면 `:as` 옵션을 사용하면 된다.

`productions/_production.html.erb`:
```rhtml
<div class="production" id="product_<%= item.id %>">
    <%= link_to selling_production_path(item.id) do %>
        <%= image_tag item.image_url %>
        <div class="name"><%= item.name %></div>
        <div class="buyer"><%= buyer %></div>
        <div class="price"><%= number_to_currency(item.selling_cost) %></div>
    <% end %>
</div>
```

`orders/show.html.erb`:
```rhtml
<%= render partial: 'productions/production', collection: @order_product, as: :item, locals: { buyer: @order.buyer } %>
```

### 3.2 counter 사용
collection으로 partial을 불렀을 때, 현재 render된 아이템의 index 값을 알면 유용한 경우가 많다.<br>(`each_with_index` 메서드처럼)

이 카운터 변수는 `#{partial_name}_counter`와 같이 사용한다.<br>
예를 들어 partial에서 `@order_product`를 랜더링하는 횟수를 `item_counter` 변수로 참조할 수 있다.

`productions/_production.html.erb`:
```rhtml
<div class="production" id="product_<%= item.id %>">
    <div class="count"><%= item_counter %></div>
    <%= link_to selling_production_path(item.id) do %>
        <%= image_tag item.image_url %>
        <div class="name"><%= item.name %></div>
        <div class="buyer"><%= buyer %></div>
        <div class="price"><%= number_to_currency(item.selling_cost) %></div>
    <% end %>
</div>
```

<br><br><br>
[Rails 가이드](http://api.rubyonrails.org/classes/ActionView/PartialRenderer.html)<br>
퍼펙트 루비 온 레일즈
