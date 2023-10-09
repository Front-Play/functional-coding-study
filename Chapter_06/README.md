## 🐝 6장 변경 가능한 데이터 구조를 가진 언어에서 불변성 유지하기

**이번장에서 살펴볼 내용**

1. 데이터가 바뀌지 않도록 하기 위해 카피-온-라이트를 적용
2. 배열과 객체를 데이터에 쓸 수 있는 카피-온-라이트 동작을 만든다.
3. 깊이 중첩된 데이터도 카피-온-라이트가 잘 동작하게 만든다.

액션은 코드 전체로 퍼진다.

### 카피 온 라이트 원칙 세 단계

1. 복사본 만들기
2. 복사본 변경하기
3. 복사본 리턴하기

```tsx
function add_element_last(array, elem) {
  var new_array = array.slice();
  // 복사본 만들기
  new_array.push(elem);
  // 복사본 바꾸기
  return new_array;
  // 복사본 리턴하기
}
```

이 함수를 다시 살펴보면

1. 배열을 복사했고, 기존 배열은 변경 놉!
2. 복사본은 함수 범위 안에 있어서 다른 코드에서는 접근불가.
3. 복사본을 변경하고 나서 함수 리턴

-> 요 함수는 읽기일까? 쓰기일까..?

!! 읽기다!! ( 데이터를 바꾸지 않고 정보를 리턴)

! 카피 온 라이트는 쓰기를 읽기로 변경

단, 값을 바꾸지 않으면 굳이 복사할 필요는 없다.

### JS 배열 훏어 보기

```tsx
var array = [1, 2, 3, 4, 5];
array[2]; // 인덱스로 값 찾기
array[2] = 'abc'; // 값 할당하기
array.length;
array.push; // 끝에 추가
array.pop(); // 끝에 있는 값 지우기
array.unshift(el); // 앞에 추가하기
array.shift(); // 앞에 있는 값 지우기
array.slice(); // 배열 복사하기
arrray.splice(inx, num); // 항목 삭제하기
```

### 쓰기를 하면서도 읽기도 하는 동작은 어떻게 해야할까?

```tsx
var a = [1, 2, 3, 4];
var b = a.shift();

console.log(b); // 1
console.log(a); // [2,3,4]
```

shift 메서드는 값을 바꾸는 동시에 배열에 첫번째 항목을 리턴한다.

그럼 어떻게 바꿀 수 있을까?  
두 가지 방법이 잇음.

1. 읽기와 쓰기 함수로 각각 분리한다.
2. 함수에서 값을 두개 리턴한다.

#### 읽기와 쓰기 동작으로 분리하기

```tsx
function drop_first(array) {
  var array_copy = array.slice();
  array_copy.shift();

  return array_copy;
}
```

#### 값을 두 개 리턴하는 함수로 만들기

```tsx
function drop_first(array) {
  var array_copy = array.slice();
  var first = array_copy.shift();

  return {
    first: first,
    array: array_copy,
  };
}
```

### 불변 데이터 구조를 읽는 것은 계산

1. 변경 가능한 데이터를 읽는 것은 액션
2. 쓰기는 데이터를 변경 가능한 구조로 만든다.
3. 어떤 데이터에 쓰기가 없다면 데이터는 변경 불가능한 데이터.
4. 불변 데이터 구조를 읽는 것은 계산
5. 쓰기를 읽기로 바꾸면 코드에 계산이 많아진다.

### 객체에 대한 카피 온 라이트

배열과 마찬가지 똑같은 단계로 구현 가능.

```tsx
function setPriceByName(cart, name, price){
  var cartCopy = cart.slice();
  // 배열 복사
  for (~){
    if(cartCopy[i].name === name)
    cartCopy[i] = setPrice(cartCopy[i], price);
    // 반복하다가 티셔츠를 찾으면 setPrice 호출
  }
  // 중첩된 항목을 바꾸기 위해
  // 카피-온-라이트 동작을 부름.
  return cartCopy;
}

function setPrice(item , new_pr i ce) {
var item_copy = 0bject.assign({} , item); // 객처| 복사
item_copy.price = new_price;
return item_copy;
```

### 요약

1. 카피 온 라이트는 값을 변경하기 전에 얕은 복사를 한다. 그리고 리턴!
   이렇게 하면 통제할 수 있는 범위에서 불변성 구현 가능!

2. 보일러 플레이트 코드(= 여러 곳에서 비슷한 코드가 반복되는 것) 를 줄이기 위해 기본적인 배열과 객체 동작에 대한 카피 온 라이트 버전을 만들어 두는 것이 좋다.
