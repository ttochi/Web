# Google Analytics Enhanced Ecommerce
Google Analytics의 전자상거래 셋팅과 관련하여 정리한다.
[구글 공식 문서](https://developers.google.com/analytics/devguides/collection/analyticsjs/enhanced-ecommerce)와 [태그매니저 문서](https://developers.google.com/tag-manager/enhanced-ecommerce)를 함께 확인하자!

## 1. 전자상거래 보고서
http://analyticsmarketing.co.kr/digital-analytics/google-analytics/1218/

전자상거래는 일종의 funnel tracking이다.
사이트에서 유저가 거래를 이루는 데 까지의 과정을 제품 별로, 목록 별로 트래킹한다.

### 1.1 쇼핑 행동 보고서

Impression Data - 제품 목록 페이지(스토어 또는 검색결과 등)에서 제품이 노출되는 것을 측정
Product Data - 노출되거나 장바구니에 담은 상품에 대한 정보
Promotion Data - 노출된 프로모션에 대한 정보
Action Data - 거래 관련 행동 정보

impressionFieldObject
productFieldObject
promoFieldObject
actionFieldObject

각 FieldObject에 대한 정보는 

### 1.2 Checkout 행동 보고서

### 1.3 제품 실적 보고서
### 1.4 매출 실적 보고서
### 1.5 제품 목록 실적 보고서

## 2. Implement

향상된 전자상거래 기능 구현 시 수집되는 데이터 항목

* 제품 노출 (Product Impressions) : 리스트 페이지(메인, 상품분류 또는 검색 결과 페이지)에서 제품이 노출되는 것을 측정
* 제품 클릭 (Product Clicks) : 리스트 페이지에서 제품 링크가 클릭되는 것을 측정
* 제품 상세 노출 (Product Detail Impressions) : 제품 상세 페이지에 제품이 노출되는 것을 측정
* 장바구니 추가/삭제 (Add/Remove from Cart) : 제품 상세 페이지 등에서 장바구니에 제품이 추가되거나 장바구니 페이지 내 장바구니에서 제품을 비우는 것을 측정
* 프로모션 노출 (Promotion Impressions) : 내부 프로모션을 통해 제품이 노출되는 것을 측정
* 프로모션 클릭 (Promotion Clicks) : 내부 프로모션을 통해 노출된 제품이 클릭되는 것을 측정
* 체크아웃 (Checkout) : 결제 프로세스 시작 단계에서 최종 구매완료 페이지 전단계까지의 결제 단계를 측정
* 구매 (Purchases) : 주문이 완료된 상세 거래 정보를 측정
* 환불 (Refunds) : 환불 정보를 측정

http://www.datalayerdoctor.com/enhanced-ecommerce-which-implementation-method/

전자상거래를 구현하는 방법은 크게 2가지가 있다. 나의 경우는 GTM의 data layer를 사용하는 방법이 태그매니저의 목적(개발자가 아닌 마케터를 위한 태그 설정 & 실제 코드와의 분리 및 간결화)에 부합하지 않는다고 생각해서 더 나은 방법에 대해 계속 찾아보는 중이다.

If you don’t have Google Tag Manager then you will have to go with option 1, the JavaScript API.

If you already use Google Tag Manager then I recommend using the data layer method of implementation. It is easier to get right first time.

The only situation where you should not use Google Tag Manager to implement Enhanced Ecommerce tracking is when you want to attach additional custom metrics or dimensions to the events.

### 2.1 Implement - GA javascript api
The first way is to put use the Google Analytics JavaScript API. You have to make calls the JavaScript API yourself, either via placing the correct code on the correct pages (either by hard-coding or through GTM).

`product detail`을 트래킹하기 위해 아래와 같은 코드를 사용한다.
```js
ga('ec:addProduct', {
    'id': production['id'],
    'name': production['name'],
    'category': production['category'],
    'brand': production['brand']
});

ga('ec:setAction', 'detail');
```

GA library에 있는 ga 함수를 두 번 연달아 부른다.
첫 번째는 GA에 product 정보를 제공하고 두 번째 함수는 GA에 해당 product 상세보기 행동이 발생했음을 전달한다.

### 2.2 Implement - GTM data layer
Google Analytics Enhanced Ecommerce enables product impression, promotion, and sales data to be sent with any of your Google Analytics pageviews and events. 

Use `pageviews` to track product impressions and product purchases
Use `events` to track checkout steps and product clicks.








