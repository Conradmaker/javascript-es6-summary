# 5.rest parameter

## 나머지 매개변수

무슨말일까요? 

저번에는 default parameter 였는데, 이번에는 rest parameter 입니다..

아주 쉽습니다. 그냥 나머지 매개변수 그 의미 그대로에요.

## 1. 소개

```javascript
function foo (a, b) {
  a = 1
  arguments[0] = 2
//여기서 arguments가 인자에도 영향을 줄까요? 
  console.log(a, arguments[0])  //a:2 ,arguments[0]:2 영향을 줬습니다.
}
foo(10, 20)
```

arguments는 불확실성이 높고 실제 배열이 아닌 유사배열 객체 이기 때문에 매우 까다롭고 난이도도 높았습니다.  

rest parameter는 이 arguments를 대체해줍니다.

아래 예제는 arguments를 이용해 호출할때 넘겨줬지만 받고 남은 나머지 매개변수를 사용하는 방법입니다.

```javascript
function f (x, y) {
  var rest = Array.prototype.slice.call(arguments, 2)
  console.log(rest)
}
f(1, 2, true, null, undefined, 10)
```

하지만 ... 즉 rest parameter를 사용하면 받아준 인자를 제외한 받지 못한 인자들을 모두 '진짜 배열'로 받아줍니다.

```javascript
const f = function (x, y, ...rest) {
  console.log(rest)  
}
f(1, 2, true, null, undefined, 10)
==================================
rest = [true, null, undefined, 10 , length:4]
```

## 2. 상세

### 1\) `...[매개변수명]`

`...`을 사용해 나머지 인자들을 배열로 받을 수 있습니다. 

### 2\) 오직 한 번, 매개변수의 가장 마지막에서만

꼭 rest parameter는 매개변수의 마지막에서 받아져야 합니다.

```javascript
const f = function (_first, ...rest, _last) {
  console.log(_first, _last)
}
f(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
===============================
Rest parameter must be last formal parameter 라는 정확한 에러가 나옵니다.
```

### 3\) 객체의 setter에서

setter에는 하나의 property에 하나의 값을 지정해주기 때문에 인자를 하나만 받습니다.

그래서 나머지를 받을수는 없지만, 

아래와 같이 사용할 경우에는 사용할수 있습니다. 

```javascript
let person = {
  name: 'name',
  age : 30,
  get personInfo(){
    return this.name + ' ' + this.age
  },
  set personInfo (...val) {
    this.name = val[0]
    this.age  = val[1]
  }
}
console.log(person.personInfo)
```

### 4\) `arguments`를 대체

모든 인자를 나머지로 받으면 arguments를 대체할수 있습니다.

```javascript
function argsAlternate (...args) {
  console.log(args.length, arguments.length)
  console.log(args[0], arguments[0])
  console.log(args[args.length - 1], arguments[arguments.length - 1])
  args[1] = 10
  arguments[1] = 20
  console.log(args[1], arguments[1])
}
argsAlternate(1, 2, 3, 4)
```

