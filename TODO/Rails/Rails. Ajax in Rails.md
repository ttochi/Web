#Working with JavaScript in Rails

Rails에서 javascript (특히 ajax) 를 다루는 방법에 대해서 알아보자!

## 3 Built-in Helpers
레일즈의 헬퍼 메소드에 ajax를 부여할 수 있다.

### form_for
form_for는 remote 옵션을 갖는다. 

```ruby
<%= form_for(@article, remote: true) do |f| %>
  ...
<% end %>
```
이를 HTML로 변환하면

```html
<form accept-charset="UTF-8" action="/articles" class="new_article" data-remote="true" id="new_article" method="post">
  ...
</form>
```

rails에서 `remote: true`를 쓰면 `data-remote="true"`가 html에 생성 --> _원리는 나중에 알아볼 것_
위의 코드는 articles 컨트롤러에 update 액션으로 간다는 것! (method: post 이므로)
해당 액션에서 js를 랜더링 하던 html을 랜더링 하던 작업을 수행

<http://www.korenlc.com/remote-true-in-rails-forms/>

**form에 remote: ture 추가하기**
form에 remote: true를 추가하면 data를 페이지 refreshing 없이 database에 넣을 수 있다.
새로고침이 아닌 자바스크립트를 이용하여 해당 페이지의 데이터 변환 내용을 refreshing 없이 바꿔줄 수 있다.
browser에서 자동적으로 render시켜주는 것은 아니므로 컨트롤러를 손봐야 한다.
