# Window Navigator
`window.navigator` 객체는 브라우저의 종류와 버전 등 웹브라우저 전반에 대한 정보를 제공하는 객체이다.

[Navigator Object Properties](http://www.w3schools.com/jsref/obj_navigator.asp)

## userAgent
`navigator.userAgent` property는 브라우저가 User-Agent HTTP 헤더에 넣어 전송하는 문자열로 appName과 appVersion의 모든 정보를 포함하며 더 자세한 정보를 추가로 담고 있다. 이 정보에 대해서는 표준화된 서식이 존재하지 않기 때문에 각 브라우저 특성에 따라 파싱해야 한다.

> 노트! User Agent 정보는 브라우저마다 다르게 제공되고 있으며 ["Mozilla/version"는 [여기](http://ohgyun.com/292)를 참고

**in Ohouse**
모바일에서 접속했는지의 여부를 확인하기 위해 아래와 같은 코드를 사용
```javascript
var ua = navigator.userAgent.toLowerCase();
window.mobile = {
    isAndroid: ua.indexOf("android") > -1,
    isIos: ua.match(/(ipad|iphone|ipod)/g) ? true : false
};
```