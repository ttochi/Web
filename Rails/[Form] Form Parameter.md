# Form Parameter

## 1. 폼에서의 parameter 전달
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


//TODO
