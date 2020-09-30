# 2.Variables

## 1. let

### 1-1. 소개

```javascript
let a = 1
function f () {
  console.log(a, b, c)
    let b = 2
    console.log(a, b, c)
    if (true) {
        let c = 3
        console.log(a, b, c)
    }
    console.log(a, b, c)
}
f()
```

1. a 는 1이며 함수 외부에서 선언했습니다.
2. 함수 f 가 있고 아래에서 f를 실행했습니다.
3. 실행한뒤 a,b,c를 출력하라고 합니다. \(line3\)
   1. 내부에 a가 없기 때문에 외부로찾아가 1이 출력
   2. b는 hoisting을 했지만, TDZ에 걸려 Reference Error
   3. c는 없으니 Reference Error
4. b에 2를 할당한뒤 a,b,c 출력
   1. a, b 는 각각 1, 2 가 출력 
   2. c는 없으니 Reference Error
5. if 문 안에는 c 가 있으니 8번line에서 1,2,3출력
6. 10번 line에서는 a,b는 1,2 c는 if문 안에 있으니 Reference Error

### 1-2. let 상세

let은 var과 같은 기능을 하지만, block scope의 영향을 받으며, TDZ의 영향도 받습니다. 

### 1\) 재할당 가능

```javascript
let a = 1
a = 2
console.log(a)
```

### 2\) 반복문 내에서의 함수 실행시

```javascript
var funcs = []
for (var i = 0; i < 10; i++) {
  funcs.push(function () {
    console.log(i)
  })
}
funcs.forEach(function (f) {
  f()       //10 이 10 번 출력
})
```

1. funcs배열에는 for문의 실행에 따라 함수 10개가 추가됩니다. 
2. forEach에는 10번 돌겠죠.

하지만 결과가 조금 이상합니다. index값은 0~9인데 왜 10이 10번 나올까요?

이유는 다음과 같습니다. 

배열에는 function\(\){console.log\(i\);}, 가 10개 들어갑니다. 

함수는 만들어 졌지만, 실행은 언제 할까요? -&gt; forEach문을 돌릴때 실행하게 됩니다. 

실행 컨텍스트도 함수를 실행할때 생성됩니다. 

그렇다면 var i는 이미 10번 돌아서 10이 되었기 때문에 10이 10번 출력된 것입니다.

그렇다면 0~9까지 출력되게 만들려면 어떻게 해야 할까요? 

i를 각각 함수내에서만 살아있도록 만들어주면 될 것 같습니다. \(var은 전역으로 올라가기 때문에\)

그래서 아래 코드는 즉시실행함수로 i를 가지고 있도록 넘겨주고 즉시실행함수는 v로 i를 받은뒤 그때 실행하도록 해주는 것입니다. 

```javascript
var funcs = []
for (var i = 0; i < 10; i++) {
  funcs.push((function (v) {
    return function () {
      console.log(v)
    }
  })(i))
}
funcs.forEach(function (f) {
  f()
})
```

아래는 es6의 경우입니다. 

let은 block scope이기 때문에 위와 같은것을 고민할 필요가 없죠? 

이렇게 되면 우리의 고민이 사라지게 된 겁입니다. 

```javascript
let funcs = []
for (let i = 0; i < 10; i++) {
  funcs.push(function () {
      console.log(i)
  })
}
funcs.forEach(function (f) {
  f()
})
```

closure에서 생성된 변수는 영원히 숨쉬기 때문에 우리는 메모리를 아낄수 있는 효과까지 let을 통해 얻을 수 있습니다. 

## 2. const

const 는 constant variable의 약자 즉, 상수변수라는 의미입니다. 

상수는 원래부터 상수였을까요?  PI라는 값은 언제부터 3.141592..... 가 되었을까요? 

누군가가 PI가 3.141592...다 라고 했을때 부터겠죠? 

프로그래밍에서 상수도 마찬가지 입니다. 

값이 할당되어야만 상수가 될 수 있는 것이죠!!

### 2-1. const 상세

### 1\) 재할당

```javascript
const PI = 3.141593
PI = 3.14  //ERROR
```

값을 할당하면 바꿀수 없습니다.

### 2\) 최초 선언시 할당하지 않으면

```javascript
const PI
PI = 3.14  //ERROR
```

값이 없는 것이 상수가 될수 있을까요? 절대 안됩니다. 

선언과 동시에 값을 할당해주어야만 하며 재할당을 할 수가 없죠. 

### 3\) 참조타입 데이터의 경우

하지만 참조타입 데이터의 경우에는 조금 다릅니다. 

```javascript
const OBJ = {
  prop1 : 1,
  prop2 : 2
}
OBJ.prop1 = 3
console.log(OBJ.prop1)
```

위 경우는 OBJ가 상수입니다. 

`OBJ.prop1 = 3` 은 상수 OBJ에 접근한 것이 아닌 OBJ안에있는 prop1이라는 property에 접근한 것입니다.  이 property는 OBJ와는 별개로 다른 공간에 저장이 되어있습니다. 

OBJ는 그 property가 저장된 공간을 참조 즉 , 주소값을 가지고 있는 것입니다. 

쉽게 말하면 OBJ는 property들이 어디 저장되 있는지 주소값을 들고 있는 그 공간을 바라보고 있으며 그 바라보는 공간을 바꿀수가 없지만, OBJ가 바라보는 property주소값이 담겨있는 공간은 상수가 아니기 때문에 값을 바꿀 수 있는 것입니다. 

> 참조형 데이터를 상수변수에 할당할 경우 상수형 데이터 안에있는 property는 상수가 아닌 것입니다.

혹시 참조형 데이터가 저장되는 원리를 모른다면 저번 글을 봐주세요. 

다음 예제입니다. 

```javascript
const ARR = [0, 1, 2]
ARR.push(3)   //[0,1,2,3]
delete ARR[1]  //[0,2,3]
console.log(ARR)  
```

즉, ARR안에 있는 property는 바꿀 수 있지만, ARR이 바라보는 주소값을 바꿀수는 없는 것입니다. 

하지만, 내부에 있는 값들도 모두 상수로 만들고 싶을수가 있습니다. 

> 해결방안: `Object.freeze()`, `Object.defineProperty()`

```javascript
const OBJ = {}
Object.defineProperty(OBJ, 'prop1', {
  value : 1,
  writable: false,
  configurable: false
})

const OBJ2 = {
  prop1 : 1
}
Object.freeze(OBJ2)
```

이런경우 OBJ.prop1 은 얼어버려서 바꿀 수 있습니다. 

하지만 문제점이 있죠. 

> 여전히 남는 문제점: nested Object의 경우... \(객체 안에 객체\)

아래 주석을 잘 따라가며 읽어주세요.

```javascript
const OBJ = {
  prop1 : 1,
  prop2 : [2, 3, 4],
  prop3 : { a: 1, b: 2 }
}
Object.freeze(OBJ) //Prop1,2,3 을 얼립니다.
OBJ.prop1 = 3      //바꿀수 없습니다.
//prop2안에 있는 데이터는 참조형 데이터입니다.
OBJ.prop2.push(5)  //OBJ안에 참조형 데이터는 얼었지만 prop2안에 참조데이터는 안얼었습니다.
OBJ.prop3.b = 3    //바꿀수 있습니다. 
console.log(OBJ)

Object.freeze(OBJ.prop2) //그래서 OBJ.prop2의 참조데이터도 얼려버립니다. 
OBJ.prop2.push(6)   //얼어버려서 넣을 수 없습니다. 
console.log(OBJ)
```

객체 안에있는 모든 property를 얼려버리고 싶다면 다음과 같이 해주어야 합니다..^^

1. Object 자체를 얼리고
2. Object 내부의 property를 순회하며 혹시 참조형이면 1번을 반복한다...
3. 이걸 재귀라고 합니다. 

이런 것을 바로 DeepFreezing이라고 합니다. 

얕은 복사 깊은 복사의 개념과 같습니다. 

깊은 복사를 해야만 immutable해지기 때문에 아주 화두가 되고 있죠...

최근에는 데이터기반의 프레임워크 React / Vue 등이 있기 때문에 한번 공부해보기를 추천합니다.

### 4\) 반복문 내부에서의 상수

아래 코드를 봐주세요.

```javascript
var obj = {
  prop1: 1,
  prop2: 2,
  prop3: 3
}
for (const prop in obj) {
  console.log(prop)
}

for (const i = 0; i < 5; i++) {
  console.log(i)
}
```

#### for in 문

const 는 분명 상수인데 반복문 for in 에서 객체 obj를 돌면서 그 prop1~3을 상수 prop에 매번 재할당 할 수 있을까요? 

놀랍게도 for in에서는 됩니다.  \(+ for of 도 됩니다\)

 왜냐하면 for in 내부의 모습이 다음과 비슷하기 때문이죠.

```javascript
let keys = Object.keys(obj);
for(let i = 0; i < keys.length; i++){
    const prop = obj[keys[i]];
    console.log(prop);
}
```

#### for 문

for 문은 또 다릅니다.

그러니 가장 기본적인 for문에서는 let을 사용해 주어야만 합니다. 

## 3. 공통사항

#### 1\) 유효범위

유효범위는 block scope내부입니다.

```javascript
{
  let a = 10
  {
    const b = 20
    console.log(b)
  }
  console.log(a)
  console.log(b)
}
console.log(a)
```

#### 2\) 재선언 \(재정의\)

```javascript
var a = 0
var a = 1
console.log(a)  //1

let b = 2
let b = 3
console.log(b) //Error

const c = 4
const c = 5
console.log(c) //Error

var d = 4
let d = 5
const d = 6
console.log(d)  //Error
// 이미 선언된 변수명은 다시 사용될 수 없습니다.
```

{% hint style="warning" %}
var과 let을 병행해서 사용하면 속도가 느려지니,

const / let을 쓰거나, var 하나만 사용해야 합니다.

당연히 const / let을 사용하는 것이 좋겠죠?
{% endhint %}

> 프론트 앤드 개발환경에서는 객체를 주로 다루기 때문에 값 자체의 변경이 필요한 예외적인 경우를 제외하면 const를 사용하는 것이 좋습니다.

참고로 첫번째 var의 경우에는 hoisting되어서 다음과 같은 모습이기 때문에 가능합니다.

```javascript
var a
a = 0
a = 1
```

#### 3\) 초기화되기 전 호출

```javascript
{
  console.log(a) //Error 실제 Hoisting은 됬지만 TDZ
  let a = 10
  {
    console.log(b) //Error 실제 Hoisting은 됬지만 TDZ
    let b = 20
  }
}

{
  console.log(a) //Error 실제 Hoisting은 됬지만 TDZ
  const a = 10
  {
    console.log(b) //Error 실제 Hoisting은 됬지만 TDZ
    const b = 20
  }
}
```

Hoisting이 안되는 것이 아닙니다!  안된 것 처럼 보이는 것이죠!

#### 3\) TDZ \(Temporal Dead Zone, 임시사각지대\)

hoisting되어도 아무것도 못하게끔 만들어줍니다. 

```javascript
{
  let a = 10
  {
    console.log(a)
    let a = 20
  }
}
```

```javascript
{
  const a = 10
  console.log(a)
  {
    console.log(a)
    const a = 20
    console.log(a)
  }
}
```

#### 4\) 전역객체의 프로퍼티?

```javascript
var a = 10
console.log(window.a)    //10
console.log(a)    //10
delete a  //false
console.log(window.a)  //10
console.log(a)  //10

window.b = 20
console.log(window.b)
console.log(b)
delete b
console.log(window.b)
console.log(b)
```

전역공간에서 선언한 var 는 전역 변수임과 동시에 전역 객체의 프로퍼티가 됩니다...

그렇게 때문에 삭제가 안되는 것입니다. 

아래 코드를 보세요.

```javascript
window.a=10;
console.log(a) //10
delete a  //true
console.log(a , window.a) //Error, undifined
=================================
var a =10;
console.log(a , window.a); //10,10
delete a //false
delete window.a //false
```

하.. var 정말 왜그럴까요? 이상한 아이입니다. 

window.a 로 선언한 변수 즉, 전역객체의 프로퍼티는 삭제가 가능한데 var은 안됩니다. 

그래서 전역 변수의 선언을 "최소화" 하고 왠만하면, 함수scope로 감싸주고, 여러가지 안전장치를 마련해야 했습니다. 

하지만 let const 에는 다 필요 없습니다. 전역공간에서 변수를 선언해도 전역객체의 property에는 들어가지 않습니다. 

```javascript
let c = 30
console.log(window.c)  //undifined
console.log(c)   //30
delete c //false  window.c가없기 때문에
console.log(window.c)  //undifined
console.log(c)   //30

const d = 40
console.log(window.d)  //undifined
console.log(d)   //40
delete d //false  window.d가없기 때문에
console.log(window.d)  //undifined
console.log(d)   //40
```

> delete명령어 자체가 객체의 property를 지우라는 명령어 입니다. 
>
>  delete b 는 `delete (window).b` 입니다.

자 이제 더더욱 let과 const 를 써야하는 이유가 명확해졌습니다!!

