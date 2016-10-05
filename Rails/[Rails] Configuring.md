# Configuring
Rails 어플리케이션의 초기화 설정을 할 수 있는 곳은 아래와 같다.

* config/application.rb
* config/environment/*
* initializer
* After-initializer

이들은 Rails 프로젝트 자체가 로드되기 전에 실행되는 파일이다.
[여기](http://guides.rorlab.org/configuring.html)를 참조하며 설정하자

## 전반적인 설정 작업
주로 `config/application.rb`와 `config/environment/*`에 프로젝트의 전반적인 설정 정보를 넘겨준다.

## Initializer
initializer는 config/initialzers 폴더에 저장되어있는 모든 Ruby 파일을 의미한다.
Rails는 프레임워크와 모든 gem을 불러온 직후에 initialzer를 부른다.

> Rails는 initializer를 하위의 폴더를 recursive로 탐색하여 실행한다.
> --> 하위 폴더를 생성하여 initialzer를 정리해줘도 됨.
