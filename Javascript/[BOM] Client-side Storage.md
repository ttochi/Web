# Client-side Storage
웹사이트의 정보를 저장하기 위해 반드시 서버를 사용하는 것만은 아니다.
클라이언트의 저장공간도 충분히 좋은 저장소로서 역할을 할 수 있으며 그 방법으로는 `Cookie`나 `html5`의 `localStorage`, `sessionStorage`가 있다.

## Cookie?
Cookies are data, stored in small text files, on your **computer**.

When a web server has sent a web page to a browser, the connection is shut down, and the server forgets everything about the user.

Cookies were invented to solve the problem **"how to remember information about the user"**

When a browser requests a web page from a server, cookies belonging to the page is added to the request. This way the server gets the necessary data to "remember" information about users.

쿠키의 경우 저장 가능한 공간의 크기가 가장 작고 하나의 텍스트 형태로 저장된다. 그렇기 때문에 비교적 간단한 텍스트 타입의 데이터를 저장하는데 용이하다.

### Usage in Javascript
javascript에서 `document.cookie` property를 통해 cookie를 다룬다.

**create cookie**
```javascript
document.cookie = "username=John Doe";
```

**with expiry date**
cookie 데이터가 만료되는 시간을 설정할 수 있다.
```javascript
document.cookie = "username=John Doe; expires=Thu, 18 Dec 2013 12:00:00 UTC";
```

**with path**
cookie가 속하는 path 경로를 지정해줄 수도 있다.
```javascript
document.cookie = "username=John Doe; expires=Thu, 18 Dec 2013 12:00:00 UTC";
```

**read cookie**
`document.cookie`는 cookie 데이터를 하나의 string으로 return한다.
```javascript
var x = document.cookie;
```

**delete cookie**
expiry date를 지난 date로 설정하면 삭제된다.
```javascript
document.cookie = "username=; expires=Thu, 01 Jan 1970 00:00:00 UTC";
```

### javascript Function example
cookie data를 설정하고 가져오는 함수 예시이다.
```javascript
function setCookie(cname, cvalue, exdays) {
    var d = new Date();
    d.setTime(d.getTime() + (exdays*24*60*60*1000));
    var expires = "expires="+d.toUTCString();
    document.cookie = cname + "=" + cvalue + "; " + expires;
}

function getCookie(cname) {
    var name = cname + "=";
    var ca = document.cookie.split(';');
    for(var i = 0; i < ca.length; i++) {
        var c = ca[i];
        while (c.charAt(0) == ' ') {
            c = c.substring(1);
        }
        if (c.indexOf(name) == 0) {
            return c.substring(name.length, c.length);
        }
    }
    return "";
}
```

### in Ohouse
모바일에서 앱으로보기 팝업이 뜨며, close를 눌렀을 때 하루동안 안보이게 한다.
cookie에 `close_deep_link=closed; expires=todayDate + 하루` 로 설정

## Local/Session Storage
With local storage, web applications can store data locally within the user's **browser**.

Before HTML5, application data had to be stored in cookies, included in every server request. Local storage is more secure, and large amounts of data can be stored locally, without affecting website performance.

Unlike cookies, the storage limit is far larger (at least 5MB) and information is never transferred to the server.

### Usage in Javascript
* window.localStorage - stores data with no expiration date
* window.sessionStorage - stores data for one session (data is lost when the browser tab is closed)

```javascript
sessionStorage.setItem("domain", "webisfree.com");
//  domain이란 키(key) 값을 사용하여 해당 텍스트를 저장함

sessionStorage.getItem("domain");
// 키에 저장된 값을 반환. 여기서는 webisfree.com 출력됨

sessionStorage.removeItem("domain");
// domain 키와 데이터 모두 삭제

sessionStorage.clear();
// 저장된 모든 값 삭제
```

<br><br><br>
[jquery-cookie](https://github.com/carhartl/jquery-cookie) 플러그인
[w3shools cookie](http://www.w3schools.com/js/js_cookies.asp)
[w3shools local storage](http://www.w3schools.com/html/html5_webstorage.asp)

