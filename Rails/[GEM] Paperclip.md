# Paperclip

Paperclip gem은 대표적인 이미지 업로드 gem이다.<br>
[Paperclip Github](https://github.com/thoughtbot/paperclip)<br>
Paperclip을 이용하여 이미지를 아마존 s3에 업로드하는 작업을 진행해보자!

## 1. Installation
paperclip을 사용하기 전에 반드시 `ImageMagick`을 설치해줘야 한다.<br>
`ImageMagick`은 command-line을 통해 이미지를 쉽게 변환 및 수정할 수 있는 프로그램이다.
```bash
sudo apt-get install imagemagick
```

> 서버에서도 미리 설치해야 하는 점 잊지 말자!

다음으로 `Gemfile`에 아래 젬들을 설치하고 `bundle install`
```ruby
gem 'paperclip'
gem 'aws-sdk', '~> 2.3'
```

> `aws-sdk`는 s3 업로드를 위한 gem이다.

## 2. Migration 수정
paperclip을 위한 database field를 추가해야한다.
```bash
rails g migration add_image_to_groups
```

이제 `attachment`라는 type을 갖는 `image` field를 추가하자.
```ruby
add_attachment :groups, :image
```

`add_attachment`는 실제로는 아래의 4개의 databse field를 생성한다.

* image_file_name
* image_content_type
* image_file_size
* image_updated_at

## 3. Model 수정
이미지 업로드를 사용하기 위해 paperclip을 사용하는 Model에 업로드한 파일을 정의해야 한다.
```ruby
class Group < ActiveRecord::Base
    # ...중략...
    has_attached_file :image, styles: { small: '64x64', med: '100x100', large: '200x200' }

    validates_attachment_content_type :image, content_type: /\Aimage\/.*\Z/
end
```

### Define attached file
`has_attached_file`을 이용하여 attached file을 정의한다.<br>
또한 image를 resize하는 `style` 옵션도 받는다.<br>
image의 style의 이름과 사이즈는 필요한 대로 customize해서 사용하라.

> 사이즈 지정은 `ImageMagick`의 [옵션](http://www.imagemagick.org/Usage/resize/)을 사용한다.

### Validation
paperclip을 사용하려면 validation을 해줘야 한다.<br>
present? type? size?
```ruby
validates_attachment_presence :image
validates_attachment_content_type :image, content_type: /\Aimage\/.*\Z/
validates_attachment_size :image, in: 0..10.kilobytes
```

이를 한번에 정의할 수도 있다.
```ruby
validates_attachment :avatar, presence: true,
    content_type: { content_type: "image/jpeg" },
    size: { in: 0..10.kilobytes }
```

validation을 하지 않는 상황이라면 명시해줘야 한다.
```ruby
do_not_validate_attachment_file_type :image
```

## 4. View 수정

### form 수정
`multipart` 옵션을 주고 입력 field는 `file_field`를 사용하자.
```rhtml
<%= form_for @group, html: { multipart: true } do |f| %>
    <%= f.file_field :image, required: true %>
```

> controller에서 form에 대한 strong parameter는 간단히 `:image`로 지정해주면 된다.

### image display
```rhtml
<%= link_to image_tag(@group.image.url(:med)), @post.image.url %>
```

### default image
`missing.png`로 default image를 설정할 수 있다.

## 5. S3 업로드 설정
지금까지의 작업으로는 업로드한 이미지가 모두 프로젝트 디렉토리 `public/system`에 저장된다.<br>
프로젝트에 파일을 모두 저장하게 되면 사이즈가 너무 커지므로 아마존 s3 파일 서버를 이용해보자!<br>
간단한 paperclip configuration으로 아마존 s3에 업로드 시킬 수 있다.

config/environment:
```ruby
config.paperclip_defaults = {
      storage: :s3,
      s3_credentials: {
            bucket: ENV.fetch('S3_BUCKET_NAME'),
            access_key_id: ENV.fetch('AWS_ACCESS_KEY_ID'),
            secret_access_key: ENV.fetch('AWS_SECRET_ACCESS_KEY'),
            s3_region: ENV.fetch('AWS_REGION')
      }
}
```

> Seoul의 region은 `ap-northeast-2`이다.

업로드한 파일의 기본 path를 지정할 수 있다.<br>
아래와 같이 설정하면 `/groups/images/1/large.png`에 파일이 저장된다.

config/paperclip.rb:
```ruby
Paperclip::Attachment.default_options[:path] = '/:class/:attachment/:id/:style.:extension'
```

만약 "The bucket you are attempting to access must be addressed using the specified endpoint." 같은 에러가 발생하면 아래와 같이 endpoint를 명시하자

config/paperclip.rb:
```ruby
Paperclip::Attachment.default_options[:s3_host_name] = 's3.ap-northeast-2.amazonaws.com'
```

<br><br><br>
[참고사이트](https://richonrails.com/articles/getting-started-with-paperclip)
[참고사이트](https://devcenter.heroku.com/articles/paperclip-s3#international-users-additional-configuration)