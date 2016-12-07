# [Model] Basic

## 1:N 관계
`Bulletin`과 `Post`가 1:N의 관계를 가질 때 model 파일에 다음과 같이 관계를 선언해준다.

```ruby
class Bulletin < ActiveRecord::Base
  has_many :posts, dependent: :destroy
end
```
```ruby
class Post < ActiveRecord::Base
  belongs_to :bulletin
end
```

Bulletin 모델 --> `has_many :posts` (복수형)<br>
Post 모델 --> `belongs_to :bulletin` (단수형)

`has_many`의 경우 `dependent:` 옵션을 지정할 수 있다.<br>
`:destroy` --> bulletin 레코드를 삭제할 때 이 게시판에 속하는 모든 posts도 동시에 삭제

## Active Record를 통한 두 모델의 연결
관계 선언만으로 DB 테이블이 자동으로 연결되지 않음! 꼭 foregin key 설정해야 함<br>
rails에서는 기본적으로 모든 테이블의 primary key가 id값이다

active recode로 두 모델의 DB 테이블을 물리적으로 연결하는 법!<br>
posts table에 `bulletin_id` (foregin key)라는 필드를 추가해준다.
```
rails g migration add_bulletin_id_to_posts bulletin_id:integer:index
```

1. `add_bulletin_id_to_posts` 라는 migration file을 생성
2. 필드명과 데이터명을 이어서 생성 `bulletin_id:integer`
3. `index` 옵션을 지정하여 DB에서 빠른 검색을 가능하게 함

이렇게 생성된 migration file에 대해 db:migrate 작업을 수행

## Migration File
일반적으로 SQL을 수정하게 되면 다른 개발자들에게 수정 사항을 알려줘야한다. 그리고 다음에 제품을 배포할때도 변경 사항이 실행되는지 확인해야 한다.

Active Record는 이미 실행된 migration을 추적합니다. 따라서 DB 내용을 수정하게 된다면 소스를 업데이트 하고 rake db:migrate 명령을 실행하기만 하면 된다. Active Record는 실행해야 할 migration만 작업을 진행합니다.

그리고 데이터베이스의 구조에 부합하는 db/schema.rb 파일도 갱신합니다.

새로 생성한 migration file은 다음과 같다
```ruby
class AddBulletinIdToPosts < ActiveRecord::Migration
  def change
    add_column :posts, :bulletin_id, :integer
    add_index :posts, :bulletin_id
  end
end
```

add_column 메소드와 add_index 메소드를 수행한다.

일반적으로 이미 제품에 적용된 migration file을 수정하면 rollback을 수행하기 어렵기 때문에 문제가 생긴다. 따라서 필요한 변경을 수행하는 새로운 migration file을 만들어 소스 관리 도구에 커밋되지 않은 새로운 마이그레이션을 수정하는 편이 상대적으로 무해함.

bulletin_id를 바로 create_posts migration file 내에서 수정하지 않는 이유!

