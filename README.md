
# 배달의 민족 St 웹어플리케이션 만들기 연습
> 서울대학교 벤처창업 웹프로그래밍 기말고사 [공개문제](https://gist.github.com/allieus/fe16998add86716b4825ec56205e00ce)를 활용하여 django를 연습한 내용입니다.
> 요구사항과 참고자료는 아래와 같습니다.

### 출처
+ 서울대학교 벤처창업 웹프로그래밍 기말고사 [공개문제](https://gist.github.com/allieus/fe16998add86716b4825ec56205e00ce)
+ [Askdjango 이진석님 풀이코드](https://github.com/askdjango/snu-201703-final)
+ [풀이영상](https://www.youtube.com/watch?v=DT2TKvnZREQ)

### 체크 포인트

+ 모든 기능은 동작해야합니다.
+ 강의 시간에 다뤄진 코드 중심으로 작성하기.
+ DB에 입력하는 데이터는 최대한 실제 서비스에 가깝게 입력하기.
+ 커스텀 유효성검사 루틴을 적용하지 않으셔도 됩니다.
+ 백엔드 코드에 포커스를 둡니다. 사이트 UI를 이쁘게 꾸미더라도 가산점은 없습니다.
+ 모든 뷰에 대한 링크에는 필히 URL Reverse 적용
+ 클래스명은 CamelCase, 함수명/필드명은 snake\_case를 지켜주세요.
+ URL 정규표현식은 타이트하게 입력해주세요.
+ 모든 템플릿은 다음 최소 스펙으로 구현되어야만 하며, 필히 템플릿 상속을 활용해야합니다.

```html
<!doctype html>
<html>
    <head>
        <meta charset="utf-8" />
        <title>벤처창업웹프로그래밍2 - 2017</title>
    </head>
    <body>
    </body>
</html>
```


### 시험문제

#### 모델 및 제약사항

+ Shop (배달가게)
+ Item (상품) : 가게 별로 취급하는 상품목록
    - 한 상품은 한 가게에만 귀속됩니다. 한 상품이 다수 가게에 귀속될 수 없습니다.
+ Order (유저 주문)
    - 유저는 한 가게에 방문하여, 하나의 주문에서 다수의 상품을 구매할 수 있습니다.
    - 각 상품은 한 주문 내에서 1개씩 구매함을 가정합니다.
    - 생성된 주문은 수정할 수 없습니다. 즉 수정기능은 구현하지 않아도 됩니다.

```python
# 필수필드

class Shop(models.Model):
    name =                  # 이름
    tel =                   # 전화번호
    addr =                  # 주소

class Item(models.Model):
    shop =                  # 가게
    name =                  # 이름
    price =                 # 가격

class Order(models.Model):
    shop =                  # 가게
    user =                  # 주문한 유저
    item_set =              # 주문한 상품 목록 (Hint: ManyToManyField)
    created_at =            # 생성일시
```


#### 구현할 URL

| 구현할 URL             | 뷰                                  |
| ---------------------- | ----------------------------------- |
| /accounts/login/       | django.contrib.auth.views.login 뷰  |
| /accounts/logout/      | django.contrib.auth.views.logout 뷰 |
| /accounts/signup/      | accounts.views.signup 뷰            |
| /accounts/profile/     | accounts.views.profile 뷰           |
| /baemin/               | baemin.views.shop_list 뷰           |
| /baemin/(shop_id)/new/ | baemin.views.order_new 뷰           |


#### 상세 내용

+ 장고 프로젝트 **final** 생성
+ 회원가입/로그인을 django.contrib.auth 기본 스펙으로 구현
    - 웹 스크린샷 찍기
+ 장고앱 **baemin** 생성
+ baemin 앱 모델 등록/마이그레이션
+ admin페이지 내에서 배달가게/상품 정보를 등록
    - 가게 3개 이상 등록
    - 가게 별로 상품을 3개 이상 등록
    - 웹 스크린샷 찍기
+ /baemin/ 주소를 통해 가게 목록 노출
    - 목록 : 가게이름/전화번호/주소/**주문하기** 노출

```html
<table>
    <thead>
        <tr>
            <th>가게명</th>
            <th>전화번호</th>
            <th>주소</th>
            <th>주문하기</th>
        </tr>
    </thead>
    <tbody>

        <!-- 가게 수만큼 반복 -->

        <tr>
            <td>만리장성</td>
            <td>02-1234-1234</td>
            <td>역삼동 123번지</td>
            <td><a href="#">주문하기</a></td>  <!-- 아래 항목에서 구현 예정 -->
        </tr>

        <tr>
            <td>천리향</td>
            <td>02-345-1312</td>
            <td>서초동 987번지</td>
            <td><a href="#">주문하기</a></td>  <!-- 아래 항목에서 구현 예정 -->
        </tr>

    </tbody>
</table>
```

+ 유저는 /baemin/가게pk/new/ 주소를 통해 주문하기
    - 주문은 로그인한 유저에게만 허용됩니다. 로그인하지 않은 유저는 로그인 페이지로 이동해야합니다.
    - 유저에게는 주문상품 필드만 입력받고, 가게/로그인유저 필드는 필히 코드를 통해 자동입력처리되어야만 합니다.
    - 주문에 성공하면 /baemin/으로 이동
    - Hint: ModelForm을 활용
+ 유저는 /accounts/profile/ 에서 본인의 주문목록 확인
    - 목록 : 가게명, 주문상품명 목록, 총액, 주문일시
    - 이제 주문을 하면, /baemin/이 아닌 /accounts/profile/ 로 이동토록 수정
+ 템플릿에 회원가입/로그인/로그아웃 링크 노출



#### 주문저장 시 참고 코드

```python
if form.valid():
    order = form.save(commit=False)
    order.shop = shop
    order.user = request.user
    order.save()

    # 필히 호출. form.save() 내에서 save_m2m()이 호출되는데, commit=False시에는 호출되지 않음.
    form.save_m2m()

    return redirect('profile')
```

---

### 개인 연습 결과
![스크린샷 2017-06-17 오후 12.34.20](http://i.imgur.com/ZPNFrd0.png)
![스크린샷 2017-06-17 오후 12.35.27](http://i.imgur.com/ACWgTAS.png)
![스크린샷 2017-06-17 오후 12.35.44](http://i.imgur.com/rQekCsV.png)
