# Nested Form
다른 모델과 relationship을 갖는 모델에 대해 create 혹은 update 될 때, 모델 자체의 생성, 수정과 함께 다른 모델과의 관계 생성을 하나의 form에서 처리해야 하는 경우가 있다.
이러한 경우에 대해서 다뤄보고자 한다!

## 1. Accepts nested attributes
**all the goods**의 `Group`과 `Member`과 `Item` 모델을 예시로 한다.
```ruby
class Group < ActiveRecord::Base
    has_many :members, dependent: :destroy
    accepts_nested_attributes_for :members
end
```

> `accepts_nested_attributes_for`를 선언해줌으로써 `Group` controller에서 `members_attributes` 메소드를 사용하여 `Member` 모델의 create, update, destroy를 가능하도록 한다.

```ruby
class Member < ActiveRecord::Base
    belongs_to :group
    has_many :items, through: :corresponds
    has_many :corresponds, dependent: :destroy
end
```

```ruby
class Item < ActiveRecord::Base
    has_many :members, through: :corresponds
    has_many :corresponds, dependent: :destroy
end
```

## 2. Nested forms
관계를 갖는 다른 addtional 모델에 대한 form 동시 처리하기 위해 `form_for` 내부에서 호출되는 helper
`fields_for`에 의해서 생성되는 객체는 폼 빌더로 `form_for`에서 생성되는 것과 비슷함.

```rhtml
<%= form_for @group do |f| %>
  <%= f.text_field :name %>
  <%= f.text_field :img %>
  <%= fields_for :members do |ff| %>
    <%= ff.text_field :name %>
  <% end %>
<% end %>
```

association에서 nested attribute를 accept하면 `fields_for`는 모든 assciation에 있는 element들을 render한다.

즉, 그룹 멤버가 5명으로 등록되어있다면 5개의 fields_for가 render 된다. 하지만 `new` 액션의 경우 등록되어있는 멤버가 없기 때문에 처음에 몇 개의 fields set을 보여줄 지 `controller`에서 설정해주어야 한다.

```ruby
def new
    @group = Group.new
    #.times { @group.members.build }
end
```

## 3. Permitting parameter

```ruby
private
    def group_params
        params.require(:group).permit(:name, :img, members_attributes: [:name, :id])
    end
```

모든 attribute 값을 넘긴다면 아래와 같이 `attribute_names`를 써도 된다.
```ruby
private
    def group_params
        params.require(:group).permit(Group.attribute_names + [members_attributes: Member.attribute_names])
    end
```

> 주의! `members_attributes`의 `id`는 필수적으로 넘겨줘야 한다.

이때 parameter가 넘기는 값은 다음과 같다.
```
"group" => { 
    "name"=>"exo", 
    "img"=>"exo.jpg", 
    "members_attributes" => {
        "0" => { 
            "name"=>"첸", 
        }, 
        "1" => {
            "name"=>"디오", 
        }
    }
}
```

## 4. Removing Objects
모델의 `accepts_nested_attributes_for`에 `allow_destroy: true` 옵션을 준다.
```ruby
class Group < ActiveRecord::Base
    has_many :members, dependent: :destroy
    accepts_nested_attributes_for :members, allow_destroy: true
end
```

whitelist parameter에 `_destroy` 필드를 추가로 넘겨준다.
```ruby
private
    def group_params
        params.require(:group).permit(:name, :img, members_attributes: [:id, :name, :_destroy])
    end
```

`_destroy` 필드가 value값으로 `true`를 가지면 해당 object는 destroy 된다.
```rhtml
<%= f.fields_for :members do |ff| %>
    <div class="member_field">
        <%= ff.text_field :name %>
        <%= ff.hidden_field :_destroy %>
        <div class="remove_member">-</div>
    </div>
<% end %>
```

> javascript로 `_destroy` 필드에 value값을 부여하거나 `checkbox` 이용 등등~

## 5. Empty field 처리
user가 fill in 하지 않은 필드에 대해서 무시하려면 `accepts_nested_attributes_for`에 `:reject_if` 옵션을 아래와 같이 추가해준다.
```ruby
class Group < ActiveRecord::Base
    has_many :members, dependent: :destroy
    accepts_nested_attributes_for :members, allow_destroy: true, reject_if: lambda {|attributes| attributes['name'].blank?}
end
```

## 6. Adding Fields
유저가 'add' 버튼을 누를 때마다 동적으로 필드를 추가시키는 것은 Rails에서 따로 제공하지는 않는다. 새로운 field set을 추가할 때 key of the associated array가 unique 해아한다는 것에 주의하자!

현재 member field의 `html` 코드는 다음과 같다.
```html
<input type="text" name="group[members_attributes][0][name]" id="group_members_attributes_0_name">
<input type="hidden" value="false" name="group[members_attributes][0][_destroy]" id="group_members_attributes_0__destroy">
```

새로운 필드를 추가하기 위해 위의 `id`와 `name`이 갖는 숫자 값을 unique 하게 줘야한다는 것!


<br><br><br>
[RailsGuide](http://guides.rubyonrails.org/form_helpers.html#building-complex-forms) 참고