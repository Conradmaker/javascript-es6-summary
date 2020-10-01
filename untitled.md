# 7.Enhanced object

이번에는 향상된 객체에 대하여 알아보겠습니다. 

정확히는 향상된 객체의 기능이라고 해야겠네요. 

## 1. shorthand properties \(프로퍼티 축약\)

### 1-1. 소개

똑같은 key와 value를 가질때 과거에는 아래와 같이 코드를 적용해줬습니다.

```javascript
var x = 10
var y = 20
var obj = {
  x: x,
  y: y
}
```

이제는 이렇게 생략해줘도 자동으로 `x : x;`이렇게 적용된답니다.

```javascript
const x = 10
const y = 20
const obj = {
  x,
  y
}
```

### 1-2. 상세

프로퍼티의 key와 value에 할당할 변수명이 동일한 경우 value 생략 가능.

### 1-3. 활용

#### 1\) 함수에서 객체를 리턴할 때

파일명을 받아서 파일명과 확장자를 분리하는 함수입니다. 

```javascript
const convertExtension = function (fullFileName) {
  const fullFileNameArr = fullFileName.split('.')
  const filename = fullFileNameArr[0]
  const ext = fullFileNameArr[1] && fullFileNameArr[1] === 'png' ? 'jpg' : 'gif'
  return {
    filename,
    ext
  }
}
convertExtension('abc.png')
=============================
{filename:'abc',ext:'png'}
```

#### 2\) destructuring assignment

비구조화 할당은 정말 자주 사용되죠. 

key명과 똑같은 변수명으로 value를 할당하겠다 이말입니다.

뒤에서 다시 알아볼 것입니다.

```javascript
const {
  name,
  age
} = {
  name: 'conrad',
  age: 25
}
console.log(name, age)
======================
'conrad',25
```

## 2. concise methods \(간결한 메소드\)

### 2-1. 소개

메소드를 축약해서 쓸 수 있습니다. 

과거에는 getName이라는 메소드를 만들때 아래와 같이 해줬습니다.

```javascript
var obj = {
  name: 'foo',
  getName: function () { return this.name }
}
```

이제는 아래와 같이 사용할 수 있습니다. **shorthand properties** 와 같은 맥락이죠?

```javascript
const obj = {
  name: 'foo',
  getName () { return this.name }
}
```

다만 차이점이 있습니다. 두가지 방법으로 메소드를 선언해 콘솔로 출력해보면 다음과 같습니다.

![arguments&#xC640; caller&#xC5D0; &#xCC28;&#xC774;&#xAC00; &#xC788;&#xC8E0;.](.gitbook/assets/image%20%282%29.png)

이 이유는 es6로 넘어오면서 명시적으로 Error를 출력해줘서 디버깅에 유리하도록 해주기 위해 만들어진 기능입니다. 

또한 prototype이 빠져있는데 생성자 함수로써의 기능을 없애서 원래의 기능에 맞게만 사용할 수 있으며 함수가 가벼워졌습니다. 

즉 메소드로서 사용되었을때는 메소드로써의 기능 callback함수로 넘어갔을 경우에는 함수로써의 기능에 집중하는 것입니다. 

### 2-2. 상세

#### 1\) `:function` 키워드 제거

#### 2\) `super` 명렁어로 상위 클래스에 접근 가능

prototype channing상에 상위에 있는 클래스에 접근해라 이런 의미이죠.

```javascript
const Person = {
  greeting: function () { return 'hello' }
}
const friend = {
  greeting: function () {
    return 'hi, ' + super.greeting()
  }
}
Object.setPrototypeOf(friend, Person)
friend.greeting() 
```

```javascript
const Person = {
  greeting () { return 'hello' }
}
const friend = {
  greeting () {
    return 'hi, ' + super.greeting()
  }
}
Object.setPrototypeOf(friend, Person)
//friend를 instance로 하고 Person을 생성자 함수로 해라. 
//그러면 friend.__proto__={greeting:function(){}} 안에 들어감
friend.greeting() //'hi, hello'
friend.__proto__greeting() //'hello'
```

#### 3\) `prototype` 프로퍼티가 없음 -&gt; 생성자함수로?

위에서 본 내용입니다.

```javascript
const Person = {
  greeting () { return 'hello' }
}
const p = new Person.greeting()
```

#### 4\) 그밖에는 모두 기존 함수/메소드와 동일

```javascript
const obj = {
  a () { console.log('obj log') },
  log () { console.log(this) }
}
console.log(obj.a.name)
setTimeout(obj.a, 1000)
obj.log()
obj.log.call([])
setTimeout(obj.log.bind('haha'), 2000)
```

## 3. computed property name \(계산된 프로퍼티명\)

### 3-1. 소개

```javascript
var className = ' Class'
var obj = {}

obj.'ab cd' = 'AB CD' //SyntaxError
. 찍고 나면 뒤에 문자열이 올 수 없습니다.

obj = {
  'ab cd': 'AB CD'  //성공
}

obj['ab cd'] = 'AB CD' //성공

var obj = {
  'A' + className: 'A급'  //SyntaxError
}

obj['A' + className] = 'A급'    //성공

const obj2 = {
  'ab cd':
}
```

규칙이 조금 햇갈리죠?

기존의 `[]`표기법은 이미 생성된 객체리터럴 안에 프로퍼티 하나를 추가할때 사용했습니다. 

즉 \[\] 를 객체가 완성\(선언\)된 이후에 사용이 되었는데 이제는 선언하는 과정에서도 사용할 수 있게 된것입니다. 

```javascript
let suffix = ' name'
let iu = {
    ['last'+suffix]: '이',
    ['first'+suffix]: '지은',
}
console.log(iu)
===========================
{last name:'이',first name:'지은'}
```

```javascript
const count = (function (c) {
  return function () {
    return c++
  }
})(0)
var obj = {
  [`a_${count()}`]:count(),
  [`a_${count()}`]:count(),
  [`a_${count()}`]:count()
}
console.log(obj)
==============================
{a_0:1, a_2:3, a_4:5}
```

1. 즉시실행 함수이며 0 을 넘깁니다. 
2. c는 처음에 0 인데 호출할때마다 하나씩 올라갑니다.

### 3-2. 상세

#### 1\) 객체 리터럴 선언시 프로퍼티 키값에 대괄호 표기로 접근 가능

#### 2\) 대괄호 내에는 값 또는 식을 넣어 조합할 수 있음 \(문은 안되요!\)

## 4. own property enumeration order \(고정된 프로퍼티 열거 순서\)

```javascript
const obj1 = {
  c: 1,
  2: 2,
  a: 3,
  0: 4,
  b: 5,
  1: 6
}
const keys1 = []
for (const key in obj1) {
  keys1.push(key)
}
console.log(keys1) //['0','1','2','c','a','b']
console.log(Object.keys(obj1)) //['0','1','2','c','a','b']
console.log(Object.getOwnPropertyNames(obj1)) //['0','1','2','c','a','b']
//넣은 순서가 바뀌었습니다. 숫자먼저순서로/문자는 순서유지

=========================================================================

const obj2 = {
  '02': true,
  '10': true,
  '01': true,
  '2': true,
}
const keys2= []
for(const key in obj2) {
  keys2.push(key)
}
console.log(keys2) //['2','10','02','01'] 뭔가 순서가 이상합니다.
console.log(Object.keys(obj2))
console.log(Object.getOwnPropertyNames(obj2))
console.log(Reflect.ownKeys(obj2))
// '숫자'에서 숫자인 문자열을 숫자로 인식하기 위해서는 첫글자가 0 이 아니어야 합니다.
//'02','01'은 문자열로 인식된 것이기 때문에 뒤에 나온 것입니다.

=========================================================================

const obj3 = Object.assign({}, obj1, obj2)
const keys3= []
for(const key in obj3) {
  keys3.push(key)
}
console.log(keys3) //['0','1','2','10','c','a','b','02','01']
console.log(Object.keys(obj3)) //['0','1','2','10','c','a','b','02','01']

//하지만 obj3를 직접 보면 구조는 아래와 같습니다.
{0:4, 1:6, 2:true, 10:true, c:1, a:3, b:5, 02:true, 01:true ...}
//즉 보기위해 그냥 obj3를 보면 순서를 따르지 않지만,
//실제로 개발을 위해 console.log()를 프로그래밍상으로 돌리면 순서를 따르게 되는것입니다.
```

#### 1\) 열거순서는 다음 규칙을 따른다.

* **number &rarr string &rarr symbol** 의 순서로 정렬된다.
* `number` key는 프로퍼티들 중 가장 앞에 위치하며, 오름차순이다.
* `string` key는 객체에 추가된 당시의 순서를 유지하면서 숫자 뒤에 위치한다.
* `Symbol` key는 객체에 추가된 당시의 순서를 유지하면서 제일 마지막에 위치한다.

#### 2\) number\(index\)로 인식하는 key는 다음과 같다.

* 0 이상의, 첫째자리가 0이 아닌 수는, 문자열로 입력해도 똑같이 숫자로 인식한다.
* 첫째자리가 0인 두자리 이상의 숫자는 문자열로 입력해야 하고, 문자열로 인식한다.
* 음수는 문자열로 입력해야 하고, 문자열로 인식한다.

**∴ 'index'로 인식할 수 있는 경우에 한해서만 작은 수부터 나열한다.**

#### 3\) 열거순서를 엄격히 지키는 경우는 다음과 같다.

* `Object.getOwnPropertyNames()`
* `Reflect.ownKeys()`
* `Object.assign()`

#### 4\) ES5 하위문법인 다음의 경우에는 정합성을 보장하지 않는다.\(브라우저마음\)

* `for in`
* `Object.keys()`
* `JSON.stringify()`

즉 한마디로 열거순서는 먼저 숫자를 오름차순 다음 문자열을 입력순서. 그다음 심볼 입력순서

