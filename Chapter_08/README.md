## 🐝 8장 계층형 설계

**이번장에서 살펴볼 내용**

1. 소프트웨어 설계에 대한 실용적인 정의를 소개
2. 계층형 설계를 이해하고 어떤 도움이 되는지
3. 깨끗한 코드를 만들기 위해 함수를 추출하는 방법
4. 계층을나눠서소프트웨어를설계하면왜더나은생각을할수있는지

### SW 설계란?

코드를 만들고, 테스트 하고, 유지보수하기 쉬운 프로그래밍 방법을 선택하기 위해 미적감각을 사용하는 것.

### 계층형 설계란?

각 계층을 정확하게 구분하는 것은 어려움.
결국 감각을 개발하고, 그 감각을 따라는 것이 중요!
이 감각은 갈고닦아야 하는 것~

### 계층형 설계 패턴

패턴 1 : 직접 구현 (8장)
패턴 2 : 추상화 벽
패턴 3 : 작은 인터페이스
패턴 4: 편리한 계층

### 패턴 1 직접 구현

// 넥타이 하나를 사면 무료로 넥타이 클립을 하나 주는 코드

```tsx
function freeTieClip(cart) {
  var hasTie = false;
  var hasTieClip = false;
  for (var i = 0; i < cart.length; i++) {
    // 장바구니를 돌면서 넥타이나 넥타이
    //클립이 있는지 확인
    var item = cart[i];
    if (item.name === 'tie') hasTie = true;
    if (item.name === 'tie clip') hasTieClip = true;
  }
  if (hasTie && !hasTieClip) {
    var tieClip = make_item('tie clip', 0);
    return add_item(cart, tieClip);
  }
  return cart;
}
```

#### 장바구니가 해야할 동작

1. 제품 추가하기

```tsx
function add_item(cart, item) {
  return add_element_last(cart, item);
}
```

2. 제품 삭제하기

```tsx
function remove_item_by_name(cart, name) {
  var idx = null;
  for (let i = 0; i < cart.length; i++) {
    if (cart[i].name === name) idx = i;
  }
  if (idx !== null) return removeItems(cart, idx, 1);
  return cart;
}
```

3.  장바구니에 제품이 있느닞 확인
    구현X
4.  합계 계산

```tsx
function calc_total(cart) {
  var total = 0;
  for (var i = 0; i < cart.length; i++) {
    var item = cart[i];
    total += item.price;
  }
  return total;
}
```

5.  장바구니 비우기
    구현X
6.  제품 이름으로 가격 설정

```tsx
function setPriceByName(cart, name, price) {
  var cartCopy = cart.slice();
  for (var i = 0; i < cartCopy.length; i++) {
    if (cartCopy[i].name === name) cartCopy[i] = setPrice(cartCopy[i], price);
  }
  return cartCopy;
}
```

7. 세금 계산

```tsx
function cartTax(cart) {
  return calc_tax(calc_total(cart));
}
```

8. 무료 배송이 되는지 확인하기

```tsx
function gets_free_shipping(cart) {
  return calc_total(cart) >= 20;
}
```

! 장바구니 안에 제품이 있는지 확인하는 함수가 있다면 계층의 저수준인 for문을 사용하지 않아도 될 듯!

```tsx
function isInCart(cart, name) {
  for (var i = 0; i < cart.length; i++) {
    if (cart[i].name === name) return true;
  }
  return false;
}
```

결과!

```tsx
function freeTieClip(cart) {
  var hasTie = isInCart(cart, 'tie');
  var hasTieClip = isInCart(cart, 'tie clip');
  if (hasTie && !hasTieClip) {
    var tieClip = make_item('tie clip', 0);
    return add_item(cart, tieClip);
  }
  return cart;
}
```

어떻게 하면 구현된 코드를 잘 읽을 수 있을까?
-> 호출 그래프 / 다이어그램을 그리자.

- 같은 계층에 있는 함수는 같은 목적을 지녀야한다.

### 요약

1. 계층형 설계는 코드를 추상화 계층으로 구성.
   -> 각 계층을 볼 때 다른 계층에 구체적인 내용은 몰라도 된다!
2. 문제 해결을 위한 함수를 구현할 때 어떤 구체화 단계로 쓸지 결정하는 것이 중요.
3. 함수 이름은 의도를 알려준다.
