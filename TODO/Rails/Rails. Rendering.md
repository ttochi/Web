#Rendering

## Rendering the default case

```ruby
# Instead of <%= render partial: "account" %>
<%= render "account" %>

# Instead of <%= render partial: "account", locals: { account: @buyer } %>
<%= render "account", account: @buyer %>

# @account.to_partial_path returns 'accounts/account', so it can be used to replace:
# <%= render partial: "accounts/account", locals: { account: @account} %>
<%= render @account %>

# @posts is an array of Post instances, so every post record returns 'posts/post' on `to_partial_path`,
# that's why we can replace:
# <%= render partial: "posts/post", collection: @posts %>
<%= render @posts %>
```

<http://api.rubyonrails.org/classes/ActionView/PartialRenderer.html>