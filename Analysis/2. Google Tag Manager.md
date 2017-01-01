# Google Tag Manager

행동, 리마케팅, 전자상거래 등에 대한 정보를 수집하기 위해 자바스크립트 코드인 `태그`를 웹에 삽입하여 Google Analytics, Firebase, Adwords와 같은 제품 서버에 정보를 보낸다. 

Google Tag Manager를 통해 사이트의 코드를 수정하는 대신 사용자 인터페이스에서 태그를 추가하고 업데이트할 수 있다.

**Why Google Tag Manager?**

사이트에 태그 삽입을 하드코딩으로 진행하게 되면 

1. 사이트 내의 많은 태그에 대해 관리가 복잡하고 어렵다. -> 따로 문서작성이 필요
2. 데이터 분석팀으로부터 매번 태그 삽입을 요청받아야 한다.
3. 삽입된 태그로 사이트 속도가 저하될 수 있다.

구글 태그매니저를 사용하면 웹사이트에 태그를 직접 추가하지 않아도 되어 데이터 분석가가 다양한 버전의 태그를 손쉽게 관리할 수 있다. 태그를 설정할 때 오류를 줄일 수 있으며 개발자가 별도로 태그 설정을 하지 않아도 된다.

## 1. 설치하기
https://tagmanager.google.com/<br>
페이지의 `head` tag의 가능한 높은 위치에 아래의 `container snippet`을 붙여넣는다.
```html
<script>
    (function (w, d, s, l, i) {
        w[l] = w[l] || [];
        w[l].push({
            'gtm.start': new Date().getTime(), event: 'gtm.js'
        });
        var f = d.getElementsByTagName(s)[0],
              j = d.createElement(s), dl = l != 'dataLayer' ? '&l=' + l : '';
        j.async = true;
        j.src =
              'https://www.googletagmanager.com/gtm.js?id=' + i + dl;
        f.parentNode.insertBefore(j, f);
    })(window, document, 'script', 'dataLayer', 'GTM-추적ID');
</script> 
```

`body` tag 바로 뒤에 `container snippet`을 붙여넣는다.
```html
<noscript>
    <iframe src="https://www.googletagmanager.com/ns.html?id=GTM-추적ID" height="0" width="0" style="display:none;visibility:hidden"></iframe>
</noscript>
```

## 2. GTM 둘러보기
모든 account는 하나 이상의 container를 가질 수 있다.
container는 tags와 언제 tag가 실행될지 결정하는 triggers를 갖는다.

### 2.1 Variables
GTM에서는 `variable` 개념을 사용할 수 있다.

1. Built-in variables - 태그 매니저에 미리 만들어진 변수
2. User-defined variables - 사용자 정의 변수로 type을 지정할 수 있음

기본적으로 GA와 연결하기 위해 GA의 추적코드를 constant 변수로 지정한다.

### 2.2 Triggers
Tag를 만들기 전에 tag를 fire하기 위한 trigger를 만들어야 한다.
Trigger는 variables & operators & values 로 만들어진다.

trigger는 유저의 액션인 event에 반응하여 실행된다.

**트리거 유형**
페이지 뷰, 클릭이벤트, 폼제출 등 tag를 fire할 트리거 유형을 선택한다.

**트리거 실행 조건**
앞서 지정한 변수를 이용하여 트리거가 발생할 조건을 설정한다.
(필요에 따라 새로운 변수를 생성할 수 있다.)

### 2.3 Tags
**태그 유형**
태그를 보낼 분석 서비스를 선택한다. (유니버셜 Google Analytics를 선택하면 된다.)
추적 ID는 앞서 constant 변수로 지정한 값을 이용한다.
이벤트의 경우 category / action / label 매개변수를 설정한다.

**트리거 설정**
해당 태그가 fire될 트리거를 설정한다.

> 태그의 naming convention은 `tag type - website name - tag location`

## 3. Data Layer
`Data Layer`는 Google Tag Manager에 전달하는 모든 정보를 담는 javascript object이다.
`Data Layer`는 key-value pair로 구성되어 GA와 같은 제품에 전달되거나 tag를 실행시킬 trigger로 사용된다.
`Events`나 `Variables`과 같은 정보가 data layer를 통해 GTM으로 전달되며, trigger는 전달받은 specific event나 variable에 기반하여 설치된다.

### 3.1 Data Layer 추가하기
두 가지 방법으로 key-value pair를 Data Layer에 삽입할 수 있다.

1. Pre-populate values
2. Push values to dataLayer

`head` tag에 아래의 스니펫을 붙여넣는다. (반드시 위에서 설명한 컨테이너 스니펫보다 위에 설정되어야 한다!)
```html
<script>
  dataLayer = [];
</script>
```

위의 코드는 dataLayer의 빈 object를 선언하는 코드이다.
아래와 같이 dataLayer의 변수를 선언할 수 있다.
```html
<script>
  dataLayer = [{
    'pageCategory': 'signup',
    'visitorType': 'high-value'
  }];
</script>
```

data layer에 정의된 variable은 유저가 해당 페이지에 체류하는 동안 지속된다.

data layer를 따로 사용하지 않고 GTM 내부의 variable만을 사용해도 GTM의 컨테이너 스니펫이 알아서 data layer object를 초기화해준다.

### 3.2 Event Handler
GTM에서는 `event`라고 불리는 data layer variable을 제공한다. `event`는 페이지뷰, 클릭, 폼 전송 등의 액션이 일어날 때 tag를 fire 하기 위해 불려진다.
```Javascript
dataLayer.push({'event': 'event_name'});
```

`variable` 또한 다음과 같은 형태로 data layer에 동적으로 push될 수 있다.
```Javascript
dataLayer.push({'variable_name': 'variable_value'});
```

변수명은 모든 페이지에서 convention이 유지되어야 한다.

> data layer의 변수는 모든 페이지에서 유지되지 않는다.



## 4. Pageview 설정












