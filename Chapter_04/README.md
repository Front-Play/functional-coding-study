## 🐝 4장 액션에서 계산 빼내기

**이번장에서 살펴볼 내용**

1. 어떻게 함수로 정보가 들어가고 나오는지
2. 테스트하기 쉽고 재사용성이 좋은 코드를 만들기 위한 함수형 기술에 대하여
3. 액션에서 계산을 빼내는 방법

재사용을 하기 위해서는 or 테스트 코드를 쉽게 만들기 위해서는 **액션과 계산, 데이터를 구분** 할 필요가있다.

액션은 코드 전체로 퍼진다.

### 함수에는 입출력이 존재

입출력은 명시적이거나 암묵적일 수 있다.

```ts
var total = 0;
// 인자는 명시적 입력
function add_to_total(amount) {
  console.log('', total);
  // 콘솔에 찍는 것은 암묵적 출력
  // 전역변수를 읽는 것은 암묵적 입력
  total += amount;
  // 전역 변수를 바꾼는 것도 암묵적 출력
  return total;
  // 리턴값은 명시적 출력
}
```

함수에 암묵적 입력과 출력이 있으면 액션이 된다!

테스트와 재사용성은 입출력과 관련!

### 액션에서 계산 빼내기

```ts
function A() {
  shopping_cart_total = 0;

  //     여기 함수에 카드에 담긴 물건을
  // 계산하는 로직과
  // 세금관련 등의 로직이 함께 존재
}
```

여기서 카트 계산과 세금? 관련으로 분리.

1. 서브루틴 추출하기!!
2. copy-on-write!
   전역 변수를 바꾸지 않고 복사본을 만들어 복사본에 새로운 값을 추가하고 리턴하자.

- 암묵적 입력값을 제거하기 위해서는
  명시적 입력값으로 바꾸자!

- 암묵적 출력을 제거하기 위해서는
  인자를 지역변수로 선언해주고, 명시적
  출력 (리턴~) 을 하자!

```ts
function add_item_to_cart(name, price) {
  add_item(shopping_cart, name, price);
  calc_cart_total();
}

function add_item(cart, name, price) {
  cart.push({
    name: name,
    price: price,
  });
}

// 암묵적 출력 없앤 버전

function add_item_to_cart(name, price) {
  shopping_cart = add_item(shopping_cart, name, price);
  calc_cart_total();
}

function add_item(cart, name, price) {
  var new_cart = cart.slice();
  new_cart.push({
    name: name,
    price: price,
  });

  return new_cart;
}
```
