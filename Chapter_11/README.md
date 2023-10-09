## 🐝 11장 일급함수 part2

**이번장에서 살펴볼 내용**

1. 함수 본문을 콜백으로 바꾸기 리팩터링에 대해 알아보자.

2. 함수를 리턴하는 함수가 가진 강력한 힘을 이해 ㅎㅎ

3. 고차 함수에 익숙해지기 위해 여러 고차 함수를 만들어봅시다.

### 배열에 대한 카피-온-라이트 리팩터링

카피-온-라이트 단계

1. 복사본을 만든다.
2. 복사본을 변경한다.
3. 복사본을 리턴한다.

10장 코드에서 함수 본문을 콜백으로 바꾸기 리팩터링을 적용해보자.

#### 1. 본문과 앞부분 , 뒷부분을 확인하기.

```js
function arraySet(array, idx, value) {
  var copy = array.slice();
  copy[idx] = value;
  return copy;
}
function push(array, elem) {
  var copy = array.slice();
  copy.push(elem);
  return copy;
}

function drop_last(array) {
  var array_copy = array.slice();
  array_copy.pop();
  return array_copy;
}
function drop_first(array) {
  var array_copy = array.slice();
  array_copy.shift();
  return array_copy;
}
```

복사하고 리턴하는 같음을 확인

#### 2. 함수 빼내기

함수 본문을 콜백으로 바꾸기

```js
//  원래 코드
function arraySet(array, idx, value) {
  var copy = array.slice();
  copy[idx] = value;
  return copy;
}

// 변경한 코드
function arraySet(array, idx, value) {
  return withArrayCopy(array);
}

function withArrayCopy(array) {
  var copy = array.slice();
  copy[idx] = value;
  return copy;
}
// 아직 index , value 정의되지 않음.
```

#### 3. 콜백 빼내기

```js
function arraySet(array, idx, value) {
  return withArrayCopy(array, function (copy) {
    copy[idx] = value;
  });
}
function withArrayCopy(array, modify) {
  var copy = array.slice();
  modify(copy);
  return copy;
}
```

콜백은 배열을 변경하는 일을 함!

#### 결과

!리팩터링으로 얻은 것

1. 표준화된 원친
2. 새로운 동작에 원칙을 적용할 수 있음.
3. 여러 개를 변경할 때 최적화.

### 함수를 리턴하는 함수

```js
// 원래 코드
try {
  saveUserData(user);
} catch (error) {
  logToSnapErrors(error);
}

try {
  fetchProduct(productId);
} catch (error) {
  logToSnapErrors(error);
}

//  반복되는 코드 캡슐화
function withLogging(f) {
  try {
    f();
  } catch (error) {
    logToSnapErrors(error);
  }
}

withLogging(function () {
  saveUserData(user);
});

widthLogging(function () {
  fetchProduct(productID);
});
```

문제점

1. 어떤 부분에는 로그를 남기는 것을 깜빡할 수 있다.
2. 모든 코드에 수동으로 `withLogging()` 함수를 적용해야한다.

->

1. 에러를 잡아 로그를 남길 수 있는 기능이 추가된 함수를 일반 함수처럼 그냥 호출하면 좋겠다!

는 생각이 들었다고 합니다~

#### 구현

```js
// 원래 코드
try {
  saveUserData(user);
} catch (error) {
  logToSnapErrors(error);
}

try {
  fetchProduct(productId);
} catch (error) {
  logToSnapErrors(error);
}
```

1. 이름을 명확하게

```js
// 원래 코드
try {
  saveUserDataNoLogging(user);
} catch (error) {
  logToSnapErrors(error);
}

try {
  fetchProductNoLoggin(productId);
} catch (error) {
  logToSnapErrors(error);
}
```

2. 중복을 없애자

1. 일단 wrapping 함수에 이름을 변경

```js
function saveUserDataWithLogging(user) {
  try {
    saveUserDataNoLogging(user);
  } catch (error) {
    logToSnapErrors(error);
  }
}

function fetchProductWithLoggin(productId) {
  try {
    fetchProductNoLoggin(productId);
  } catch (error) {
    logToSnapErrors(error);
  }
}
```

2. 익명함수로 만들어보자.

```js
function (arg) {
  try {
    saveUserDataNoLogging(arg);
  } catch (error) {
    logToSnapErrors(error);
  }
}

function (arg) { // 앞부분
  try {
    fetchProductNoLoggin(arg); // 본문
  } catch (error) {
    logToSnapErrors(error);
  } // 뒷부분
}
```

앞부분 / 본문 / 뒷부분이 들어남

3. 함수 본문을 콜백으로 바꾸자

```js
function wrapLogging(f) {
    return function(arg) { #2
        try {
            f(arg);
        } catch (error) {
            logToSnapErrors(error);
        }
    }
}
 
var saveUserDataWithLogging = wrapLogging(saveUserDataNoLogging);
```

### 요약

1. 고차 함수로 패턴이나 원칙을 코드로 만들 수 있다.
   고차 함수를 사용하지 않는다면 일일이 수작업해야한다.

2. 고차 함수로 함수를 리턴하는 함수를 만들수 있다.

3. 고차 함수를 사용하면서 잃는 것도 있다!
   중복 코드를 없애주지만 가독성을 해칠 수 있음!!!
