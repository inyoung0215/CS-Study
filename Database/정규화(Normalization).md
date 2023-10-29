# 📐 정규화(Normalization)
> ### SQL 정규화란?
**데이터베이스 설계 과정에서 중복 데이터를 최소화하고 데이터 일관성을 유지하기 위한 중요한 원칙 중 하나이다. 정규화는 데이터베이스 테이블을 여러 테이블로 분할하고, 이러한 테이블 간의 관계를 정의함으로써 데이터를 더 효율적으로 저장하고 관리하는 방법이다.**

## 정규화의 목적
---
- ### 중복 데이터 제거
정규화는 중복 데이터를 제거하여 데이터베이스 크기를 줄이고 데이터 일관성을 유지한다. 중복 데이터는 데이터베이스를 비효율적으로 만들며, 업데이트 시 데이터 불일치 문제를 초래할 수 있다.
- ### 데이터 무결성 유지
데이터베이스의 데이터 무결성을 유지하는 것은 매우 중요하다. 정규화는 이를 달성하기 위해 데이터를 더 작은 단위로 분리하고, 각 테이블 간의 관계를 활용한다. 이를 통해 데이터 무결성 규칙을 쉽게 적용할 수 있다.

<br></br>

## 정규화의 절차
---

### 1️⃣ 제 1정규형 : 1NF (First Normal Form)
>
- **원자값(Atomic Values)** : 각 열은 원자적인 값(더 이상 분해할 수 없는 값, 하나의 값)을 가져야 한다.
- **고유한 식별자(Unique Identifier)** : 각 행은 고유한 식별자(primary key)를 가져야 한다. 이 식별자를 통해 각 행을 고유하게 식별할 수 있어야 한다.
- **중복 데이터 제거(No Duplicate Rows)** : 동일한 데이터가 중복으로 나타나지 않아야 한다.

<img src="https://velog.velcdn.com/images/nyoung215/post/4432d9ce-e082-4176-b6bb-9d8c57b88098/image.png"
style="max-width: 30%; height: auto;">

#### >> 해당 테이블이 1NF를 만족하지 않는 이유!!
"과일" 컬럼에 여러 값들이 존재!


<img src="https://velog.velcdn.com/images/nyoung215/post/cbbfda23-d40d-4a59-a504-00f11cadb451/image.png"
style="max-width: 30%; height: auto;">


### 2️⃣ 제 2정규형 : 2NF (Second Normal Form)
>
- **1NF 만족 **: 1NF를 만족해야 한다.
- **부분 종속성 제거** : 테이블의 모든 컬럼이 완전 함수적 종속을 만족해야 한다. 테이블에서 기본키가 복합키(키1, 키2)로 묶여있을 때, 두 키 중 하나의 키만으로 다른 컬럼을 결정 지을 수 있으면 안된다.

<img src="https://velog.velcdn.com/images/nyoung215/post/047924bd-8ba1-43d4-be22-c172f195fbb3/image.png"
style="max-width: 45%; height: auto;">

#### >> 해당 테이블이 2NF를 만족하지 않는 이유!!
"주문번호", "상품번호" 가 키 값인 경우 "과일" 컬럼이 "상품 번호" 키에만 의존하고 주문번호에는 종속되지 않았다.

<img src="https://velog.velcdn.com/images/nyoung215/post/81a04936-05df-4da0-a2dd-505efe7c2219/image.png"
style="max-width: 70%; height: auto;">


### 3️⃣ 제 3정규형 : 3NF (Third Normal Form)
>  #### 데이터베이스 정규화의 세 번째 단계로, 2NF를 만족하면서 이행 종속성을 제거하는 것이 목적이다.
- **2NF 만족** : 2NF를 만족해야 한다.
- **기본키 의존** : 기본키가 아닌 속성들은 기본키에 의존해야 한다.
```
이행 종속성이란?
A → B, B → C면 A → C가 성립되는 것을 의미.
```

<img src="https://velog.velcdn.com/images/nyoung215/post/3b5aa9e7-bd9d-44d5-a7d2-fd21a5e4645c/image.png"
style="max-width: 70%; height: auto;">

#### >> 해당 테이블이 3NF를 만족하지 않는 이유!!
"주문번호" 가 키 값인 해당 테이블의 경우, "금액" 이 기본키가 아닌 "과일" 컬럼에 의존하고 있다.
만약 주문 번호 1번의 과일이 바나나로 변경되었다고 한다면, 주문 1번의 금액은 3,000원이 된다.
물론 과일에 맞게 금액을 다시 변경할 수 있지만, 이러한 번거로움을 해결하기 위해 제3 정규화를 해주어야 한다.

<img src="https://velog.velcdn.com/images/nyoung215/post/3608650e-4e4e-4300-8095-9a6c1f3a5aba/image.png"
style="max-width: 60%; height: auto;">

### ⏩️ BCNF (Boyce-Codd Normal Form)
> ####  주로 다중 후보 키와 관련된 테이블에서 발생한다.
- **3NF 만족** :  3NF를 만족해야 한다.
- **후보 키에 대한 함수 종속성** : 모든 결정자가 후보키가 되도록 테이블을 분해하는 것. 즉, 후보키 집합에 없는 칼럼이 결정자가 되어서는 안 된다는 뜻.
```
후보키란?
테이블 내에서 각 행을 고유하게 식별할 수 있는 열(또는 열의 조합)의 집합.
테이블에는 여러 후보 키가 존재할 수 있으며, 각 후보 키는 테이블의 특정 열(또는 열 조합)에 대한 함수 종속성을 나타낸다.
```

<img src="https://velog.velcdn.com/images/nyoung215/post/2b946cba-4bae-4253-bd87-89e976f1e691/image.png"
style="max-width: 70%; height: auto;">

#### >> 해당 테이블이 BCNF를 만족하지 않는 이유!!
"주문 번호", "과일" 이 기본키인 해당  테이블의 경우, 기본키로 "판매자" 를 알 수 있다.
하지만 같은 과일을 다른 판매자가 판매할 수도 있어서 과일-> 판매자 종속은 성립하지 않는다. 하지만 판매자가 어떤 과일을 판매하는지는 알 수 있으므로 판매자-> 과일 종속이 성립한다.
이처럼 후보키 집합이 아닌 칼럼이 결정자가 되어버린 상황을 BCNF를 만족하지 않는다고 한다.

<img src="https://velog.velcdn.com/images/nyoung215/post/7bcde563-d531-4a2c-bc4a-3d689234bd59/image.png"
style="max-width: 60%; height: auto;">

### 4️⃣ 제 4정규형 : 4NF (Fourth Normal Form)
> **보통 정규화는 BCNF 까지만 하는 경우가 많다. 그 이상 정규화를 하면 정규화의 단점이 나타날 수도 있다.**
- **다치 종속성 (Multivalued Dependency)** : 다치 종속성은 테이블의 열들 간에 다중 값 종속성을 나타낸다. 즉, 하나의 키 (주요 키 또는 후보 키)에 대한 여러 개의 값 집합이 있을 때 다치 종속성이 발생한다.
- **후보 키의 독립성** : 테이블이 여러 개의 후보 키를 가질 때, 한 후보 키의 값을 변경할 때 다른 후보 키에 영향을 주어서는 안 된다. 즉, (A, B, C)가 있을 때 A와 B 사이에 다치 종속성이 있을 때 B와 C가 독립적이어야 한다.
```
다치 종속이란?
A->B 일 때 하나의 A값에 여러 개의 B값이 존재하면 다치 종속성을 가진다고 하고 A↠B라고 표시한다
다치 종속은 최소 2개의 컬럼이 다른 컬럼에 종속되어야 하기 때문에 최소 3개의 컬럼이 필요하다.
```

<img src="https://velog.velcdn.com/images/nyoung215/post/87ba9d61-fc4d-4edd-8801-3046df30e191/image.png"
style="max-width: 70%; height: auto;">

#### >> 해당 테이블이 4NF를 만족하지 않는 이유!!
"과일" 과 "배달원" 은 독립적이지만, 각각 "주문 번호" 에 다치 종속되어버려 중복성이 생긴다.

<img src="https://velog.velcdn.com/images/nyoung215/post/02e2908a-e335-469d-8438-f769a3b4a7b2/image.png"
style="max-width: 60%; height: auto;">


### 5️⃣ 제 5정규형 : 5NF (Fifth Normal Form)
> **조인 종속성을 처리하기 위해 추가적인 정규화 단계로, 복잡성과 성능 문제로 현실적으로 잘 사용되지 않는다.**
- **4NF 만족** : 4NF를 만족해야 한다.
- **조인 종속성 (Join Dependency)** : 조인 종속성이 없어야 하고, 조인 연산을 했을 때 손실이 없어야 한다.
```
조인 종속성이란?
테이블을 여러개의 테이블으로 분해하였다가 다시 조인할 때 데이터가 손실되거나 필요없는 데이터가 생기는 경우를 말한다.
조인 종속성은 다치 종속의 개념을 더 일반화한 것이다.
```

#### 조인 종속성의 예시) 위의 4NF 정규화 후 테이블에서 조인 연산을 진행한다면, 아래처럼 조인 종속성이 생긴다.
<img src="https://velog.velcdn.com/images/nyoung215/post/620ca402-8abc-4c27-bfd3-e47fe866b750/image.png"
style="max-width: 40%; height: auto;">

![](https://velog.velcdn.com/images/nyoung215/post/b50bb8de-647f-4e8a-b19d-be4bceff584a/image.png)

#### >> 제5 정규형의 경우 예시를 들기 복잡하다.
<br></br>
## 정규화의 단점

>
**테이블의 분해로 인해 데이터베이스 내의 JOIN 연산이 많아져, 쿼리 성능이 저하될 수 있다. 특히 대규모 데이터베이스에서는 조인 비용이 높아질 수 있다.
따라서 처리 성능이 중요한 데이터에 대해 '반정규화(De-normalization)'하기도 한다. **

### 반정규화란?
데이터베이스의 성능 향상을 위하여, 데이터 중복을 허용하고 조인을 줄이는 데이터베이스 성능 향상 방법이다.
반정규화는 조회(select) 속도를 향상시키지만, 데이터 모델의 유연성은 낮아진다.

---
**래퍼런스**
https://sodayeong.tistory.com/106
https://code-lab1.tistory.com/48