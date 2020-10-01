# 4.default parameter

## 1. 소개

es6에서는 함수의 인자로 넘어오는 매게변수의 기본값을 설정해줄 수 있는 방법이 새로 나타났습니다!

es5에서는 어떻게 했었을까요? 

```javascript
const f = function (x, y, z) {
  x = x ? x : 4
  y = y || 5
  if (!z) {
    z = 6
  }
  console.log(x, y, z)
}
f(1)

f(0, null)
```

인자가 truthy한 value면 인자를 쓰고아니면 지정한 값을 써라 

이런 의미이죠?

하지만 아래의 경우에는 어떨까요? 

```javascript
const f = function (x, y, z) {
  x = x !== undefined ? x : 3
  y = typeof x !== "undefined" ? y : 4
  console.log(x, y)
}
f(0, null)
```

0은 falsy한 value이죠! 

그렇기 때문에 정확성이 떨어집니다. 

그렇기 때문에 undifined를 명시해 주어야 하며, typeof를 통해 문자열로 비교를 해주었습니다.



하지만!! es6에서는 그럴 필요가 없어졌습니다.

```javascript
const f = function (a = 1, b = 2, c = 3, d = 4, e = 5, f = 6) {
  console.log(a, b, c, d, e, f)
}
f(7, 0, "", false, null)
```

쉽죠??!!

## 2. 상세

### 1\) undefined 혹은 누락된 파라미터에 대해서만

default parameter가 동작을 합니다.

### 2\) 식?

기본값으로 식을 넣을수도 있습니다.

읽어본뒤 아래 내용들을 먼저 읽으면 이해가 가능할 것입니다.

```javascript
const f = function (x = 1, y = 3 + x) {
  console.log(x, y)
}
f()
```

또한 기본값으로 함수를 넣을수도 있습니다. 

아래의 경우에는 만약 인자가 넘어오지 않았다면 validation Error를 만들어 줍니다.

```javascript
const getDefault = function () {
  console.error('getDefault Called.')
  return 10
}
const sum = function (x, y = getDefault()) {
  console.log(x + y)
}
sum(1, 2)
sum(1)
```

### 4\) `let` 선언과 동일한 효과

원래 함수의 인자를 넣는것은 함수 안에 들어온 인자를 다음과 같이 함수 안에서만 사용할 수 있는 변수로 만들어 주는것과 동일합니다.

```javascript
const f = function (x , y , z) {
  var x = 1;
  var y = 2;
  var z = 3;
  console.log(x, y, z)
}
f(1,2,3)
```

기본값을 지정해주는 것은 함수 안에서 let으로 변수를 만들어 주는 것과 동일한 효과를 가집니다.

```javascript
const f = function (x=1 , y=2) {
  let _x = x !== undifined ? x : 1;
  let _y = y !== undifined ? y : 2;
  console.log(x, y)
}
f(1,2)
```

그렇기 때문에 식을 넣어주는 것도 전혀 문제가 되지 않습니다.

```javascript
const f = function (x = 1, y = 2 + x) {
  let z = y + 3
  x = 4
  console.log(x, y, z)
}
f()
```

### 4-1\) TDZ

단, let과 동일하게 작동을 하기 때문에 식을 넣을때에는 순서가 중요해집니다.

아래는 x가 먼저 선언되기 때문에 y에서 x를 사용해도 문제가 일어나지 않습니다. 

```javascript
const multiply = function (x, y = x * 2) {
  console.log(x * y)
}
multiply(2, 3)
multiply(2)
```

하지만 x의 기본값에 y를 넣는다면 let y가 선언되지도 않았는데 사용할 수 없겠죠?

```javascript
const multiply = function (x = y * 3, y) {
  console.log(x, y)
}
multiply(2, 3)
multiply(undefined, 2)
=======================
let x = y * 3;
let y
//변수를 선언하기 전에 변수를 사용했기 때문에 Reference Error
```

{% hint style="info" %}
참고로 Reference Error는 참조할 데이터가 없다는 의미입니다. 

\(변수명에 데이터가 들어있는 주소 즉 참조할 주소값이 없기때문에\)
{% endhint %}

### 4-2\) 기본값으로 할당하고자 하는 값이 변수일 경우 주의사항

기본값으로 할당할 값이 변수일경우 매개변수명과 그 기본값변수명이 달라야 합니다.

```javascript
let a = 10
let b = 20
function f (aa = a, b = b) {
  console.log(aa, b)
}
f(1, 2)   //default를 무시하기 때문에 문제 없음
f(undefined, 2)  //기본값 변수 a =10 이기 때문에 (10,2)
f(1) //b의 경우 let b = b 이기 때문에 TDZ에 걸립니다. 
f()  //b의 경우 let b = b 이기 때문에 TDZ에 걸립니다. 
```

let b =b 가 왜 안될까 라고 생각하신다면 아마 대입과정을 잘 몰라서 일수 있습니다. 

{% hint style="info" %}
변수를 대입할때는 오른쪽부터 읽습니다. 그 이유는 메모리가 stack 

즉, LIFO\(Last In First Out\)구조이기 때문과 연관있는데, 

```javascript
let a = a; 
============ Hoisting ==============
let a 
(let) a = a  오른쪽 a 를 왼쪽 a 에 대입해라 
```

즉 아직 정해지지 않은 a 를 a의 값으로 대입할 수 없겠죠?

그래서 TDZ에 걸려 Reference Error가 나옵니다.
{% endhint %}

### 4-3\) arguments에도 영향을 줄까?

arguments는 유사배열 객체입니다.  \(array-like object\)

객체이지만, 각 프로퍼티의 키가 인덱스이고, length라는 프로퍼티가 있는 객체입니다.

하지만 배열이 아니기 때문에 배열메소드를 사용할 수 없습니다.

arguments는 함수의 인자로 넘어온 값들로 이루어져 있습니다.

```javascript
const a = function(a = 1, b = 2, c = 3) {
    console.log(arguments)  
    console.log(a, b, c)
}
a() //{calle:,}
a(4) // {0:4, length:1}
a(4, 5) //{0:4, length:1}
a(4, 5, 6)  //{0:4, 1:5, 2:6, length:3}
```

단, default parameter에 의해 만들어진 인자는 arguments에 들어가지 않습니다.

참고로 es6환경에서는 arguments를 사용할 일이 없답니다.

