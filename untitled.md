# 9.Function

## 1. `name` property of function

```javascript
//함수선언문
function a () { }
console.log(a.name) //a
//a 가 name 프로퍼티에 들어간다.

//함수 표현식
const b = function () { }
console.log(b.name) //b
//원래는 익명함수라 name이 없었는데, 이제는 브라우저에서 변수명을 할당

//기명함수 표현식
const c = function cc () { }
console.log(c.name) //cc
//함수선언문과 동일

//Arrow Function
const d = () => {}
console.log(d.name) //d
//Arrow Function은 익명함수이기 때문에 함수표현식과 동일

//메소드
const e = {
  om1: function () {}, //기존 메소드 표현식
  om2 () {},           //concise methods
  om3: () => {}        //Arrow Function
}
console.log(e.om1.name, e.om2.name, e.om3.name) 
//om1 , om2 , om3

class F {
  static method1 () {}
  method2 () {}
}
function G(){
  G.method1 = function(){}
  G.prototype.method2 = function(){} //프로토타입에 넣어 instance가 자신것처럼 사용가능
}
const f = new F()
console.log(F.method1.name, f.method2.name)
//method1 , method2
const g = new G()
console.log(G.method1.name, g.method2.name)
// 출력x
//G.method1을 name에 할당 or method1을 name에 할당할지가 명확하기 않아서
```

name 프로퍼티는 디버깅할때 함수명을 출력해주는 용도로 사용이 됩니다. 

```javascript
const f = (a,b) => a+b 
const g = new Function('a','b','return a+b')
//위 두개는 동일
console.log(g.name)
========================
anonymous
```

new Function\(\)으로 함수를 만들면 익명함수를 만드는데 다른것과 달리 변수명 g를 넣는것이 아닌, anonymous를 넣어줍니다.

```javascript
function a () { }    //함수 선언문
const b = function () { }    //함수표현식
const h = a.bind(b)    //this 바인딩 (this를 b로해서 a를 실행하는 걸로 새로운 함수 생성)
console.log(h.name)
======================
bound a
```

바인딩이 된 a 함수이다 라는 의미

추가적으로 getter setter도 name이 들어가게 되었습니다. 

```javascript
const person = {
  _name: '재남',
  get name () {
    return this._name
  },
  set name (v) {
    this._name = v
  }
}
const descriptor = Object.getOwnPropertyDescriptor(person, 'name')
console.log(descriptor.get.name)
console.log(descriptor.set.name)
=========================================
get name , set name
```

평소에 자주 사용하지 않는 기능들이 많기 때문에 이런게 생겼구나.. 정도만 아셔도 될 것 같습니다. 

## 2. `new.target`

{% embed url="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/new.target" %}

```javascript
function Person (name) {
  if (this instanceof Person) {
    this.name = name
  } else {
    throw new Error('new 연산자를 사용하세요.')
  }
}
//Persion을 그냥 함수로써 호출하면 에러를 출력하게 만든 것입니다. 
var p1 = new Person('conrad')
console.log(p1)
// conrad

var p2 = Person('yhg0337')
console.log(p2)
//Error:new 연산자를 사용하세요  new없이 사용해서.

var p3 = Person.call({}, '곰')
console.log(p3)
//this가 {} 즉, Person의 instance가 아니기 때문에 Error

var p4 = Person.call(p1, '곰')
console.log(p4)
//p1을 this로 넘기면 위에 9번줄 p1의 name이 곰으로 바뀝니다. 
//p4는 undifined가 나옵니다.
```

new 라는 연산자를 통해 생성하게 되면 this가 instance가 된다고 했던거 아시나요? 

그렇기 때문에 new 를 쓰면 Person의 instance에 this가 들어가서 조건문을 만족시킵니다.

p4의 경우에는 new 를 쓰지 않았는데도 함수가 돌아가는데 성공했습니다. 

그렇기 때문에 new 연산자를 강제하고 싶었는데, 그걸 통과하는 케이스가 나왔기 때문에 아래의 방법이 생겨났습니다.

```javascript
function Person (name) {
  console.dir(new.target)
  if (new.target !== undefined) {
    this.name = name
  } else {
    throw new Error('new 연산자를 사용하세요.')
  }
}

var p2 = Person('yhg0337')
console.log(p1)

var p2 = Person('yhg0337')
console.log(p2)

const p3 = Person.call({}, '곰')
console.log(p3)

const p4 = Person.call(p1, '곰')
console.log(p4)
```

함수생성시 new라는 객체에 프로퍼티가 생기는 것 같지만, new.target자체가 하나의 값입니다.

또한 new 연산자를 써서 호출한 함수자체가 target이 됩니다. 즉, new.target이 있기만 하면 new 연산자를 통해 실행했다는 의미가 되겠죠.

저번에 Arrow Function은 this뿐만 아니라 new.target도 바인딩 하지 않는다고 했습니다. 

```javascript
function Person (name) {
  const af = n => {
    this.name = n //this바인딩이 없기 때문에 외부this즉 Person
    console.log(new.target) //this바인딩이 없기 때문에 외부this
  }
  af(name)
}
const p1 = new Person('conrad') //Person
const p2 = Person('yhg0337')  //undifined
```

```javascript
function Person (name) {
  this.name = name
}
function Android (name) {
  Person.call(this, name)
}
const p1 = new Android('conrad')
```

new로 Android를 만들었지만,  Android생성자 함수로 실행한 내용이 Person에 this를 바인딩해서 실행해주기 때문에 Person이 일반함수로써 실행되었고, Person에 name에 넣어지게 됩니다.

다음 예제는 안전장치를 강화한 것입니다. 

Person이 직접 new가 아닌 Android에 의해 일반함수로 실행되면 new.target에 Person이 아닌게 들어가겠죠.

```javascript
function Person (name) {
  console.log(new.target)
  if (new.target === Person) {
    this.name = name
  } else {
    throw new Error('Person 생성자함수를 new로 호출해야 해요!')
  }
}
function Android (name) {
  Person.call(this, name)
}
const p2 = new Android('conrad')
```

이를 이용하면 추상클래스처럼 사용할 수 있습니다. 

## 3. 블록스코프 내에서의 함수 선언과 호이스팅 \(브라우저 비교\)

함수가 block scope에 가둬질까요? 

```javascript
if (true) {
  a() //true
  function a () { console.log(true) } //Hoisting
}
a() //true
```

일단 이경우에는 block scope의 영향을 받지 않았죠? 

그렇다면 다음의 경우에는 어떨까요?

```javascript
a() //브라우저마다 다름.
if (true) {
  a()   //true
  function a () { console.log(true) } //Hoisting
}
```

a함수 선언문이 hoising되어 끌어올려지면 1번line에서 a를 호출해도 실행이 되어야 겠죠? 

하지만, 여러분이 크롬브라우저를 사용하고 있다면, false가 나오게 됩니다. \(+firefox\) 이건 틀린것입니다. 또한 , safari브라우저로 실행하게 되면 true가 나옵니다. 

즉, 크롬이라고 해도 전적으로 신뢰할 수는 없다!! 이것입니다.

이는 es6로 넘어오게 되면서 함수가 block scope에 영향을 받냐 안받냐에 대한 판단이 갈라지는 시점이 생겨서 입니다. 

'use strict'라는 것을 한번씩 들어보셨죠? 

```javascript
if (true) {
  a() //true
  function a () { console.log(true) }
  if (true) {
    a() //true
    function a () { console.log(false) }
  }
}
a() //true
```

```javascript
'use strict'
if (true) {
  a() //true
  function a () { console.log(true) }
  if (true) {
    a() //true
    function a () { console.log(false) }
  }
}
a() //Reference Error
```

'strict mode'가 아닌경우 즉, 'sloppy mode'의 경우에는 브라우저마다 동작이 다릅니다. 

하지만, strict모드에는 함수선언문도 block scope에 갇히게 됩니다. 

즉, 브라우저가 모두 동작이 같아지게 되겠죠. 

```javascript
a()
if (true) {
  function a () { console.log(true) }
  a()
}
a()
```

다시말해 위와 같은경우에는 원래 있던 래거시 코드 같은경우에는 원래 잘 동작했지만, 크롬과 같은 경우에 에러가 나오게 됩니다. 

자, 그러니 여러분들은 이제 es6에서 함수 선언문을 안쓰면 됩니다. 

* Arrow Function을 사용하고, 
* 객체에서는 메서드 축약형을 사용하고, 
* 생성자 함수에서는 class를 사용하면 됩니다. 
* 이제는 function키워드는 generator함수에서는 사용하면 됩니다.

어떻게든 안쓰는 쪽으로 고민을 하고 작성하면 좋습니다. 

