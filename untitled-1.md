---
description: 3.Template literal
---

# 3.Template literal

Template literal은 문자열입니다. 

문자열 선언 방식이 무엇이 있었을까요?

```javascript
var a = 'abc';
var b = "abc";
==============
var c = `abc`;
```

\` \` 은 백틱이라고 하는 것인데, a===b . a===c . b===c 입니다.

일반 선언방식 즉, ' ' , " " string literal 방식이라고 합니다. litetral 은 '문자 그대로' 의 뜻을 가지고 있습니다.

이제 template literal을 알아보죠. 

## 1. 소개

```javascript
console.log(`a
bb
ccc`)
===결과===
"a
bb
ccc"
```

위처럼 그냥 쓰면 여러줄 문자열이 됩니다. \n이 없이 말이죠!

단, 공백\(띄어쓰기 또한 문자로 인식하기 때문에 그대로 인식됩니다. 

`a+b = (a+b)`를 보여주기 위해서는 어떻게 했었나요? 

```javascript
console.log(a + '+' + b + '=' + (a+b));
```

**Template literal**을 사용하면 더 쉬워진답니다. 

```javascript
const a = 10
const b = 20
const str = `${a} + ${b} = ${ a + b }`
console.log(str)
```

`${ }`안에는 값이나 식을 넣을 수 있습니다. 

단, 문은 안됩니다. 문은 값이 될 수 없기 때문이죠.

정말 만약에 $50을 표현하고 싶다면 `$${50}`이렇게 해주면 됩니다.

## 2. 상세

### 1\) backtick \(\`\)

backtick을 사용해 사용합니다.

### 2\) multi-line

줄바꿈 처리 즉 여러줄 표현이 가능합니다. 

### 3\) string interpolation

**interpolation**은 보간이라는 의미인데 글자 사이에 뭔가를 넣어 보충한다는 뜻입니다.  `${}` 로 변수를 삽입합니다.

## 3. Details

### 1\) multi-line의 경우 들여쓰기에 주의

```javascript
const f = function () {
  const a = `abc
  def
  ghij`
  console.log(a)
}
f()
```

코드를 예쁘게 쓰기위해 들여쓰기를 해주면 결과또한 들여쓰기한 결과가 나오게 됩니다. **html태그의 &lt;pre&gt;** 처럼요.

저같은 경우는 보간을 위해서 template literal을 사용하기 때문에 문제되지 않습니다.

### 2\) ${ } 내에는 '**값'** 또는 '**식'**이 올 수 있다.

값이나 식은 결국 계산후 값이 되는것이기 때문에 들어갈 수 있지만, 

문의 경우에는 값이 나오지 않기 때문에 들어갈 수 없습니다. 

단, 조건문은 안되지만 삼항연산자는 들어갈 수 있습니다. 

```javascript
const counter = {
  current: 0,
  step: 1,
  count: function() { return this.current += this.step },
  reset: function() { return this.current = 0 }
}
console.log(`${counter.count()} ${counter.count()}
${counter.reset()} $${counter.count()}
${counter.count()}$`)
```

### 3\) 결국 문자열이므로, 자동으로 toString 처리가 된다.

보간 안에 들어간 변수의 값은 문자열이 됩니다.

```javascript
console.log(`${[0, 1, 2]}`) //0,1,2
console.log(`${{a:1, b:2}}`) //object Object
console.log(`${function(){ return 1 }}`)  //function()
console.log(`${(() => 1 )()}` + 1)  //11
```

### 4\) 중첩된 backtick 처리

backtick이 여러번 반복되면 그 범위에 대해 신경써줘야 합니다.

```javascript
console.log(`Foo ${`Bar`}`)
console.log(`Foo ${`Bar ${`Baz`}`}`)
```

### 5\) 가독성을 위해 trim 처리

html template로 쓰고 싶을 경우 아래와 같은경우 출력되는 값을 보면 들여쓰기가 이상하게 되어있죠.

```javascript
function a () {
  return `<div>
            <h1>Lorem ipsum.</h1>
          </div>`
}
=====결과=====
<div>
            <h1>Lorem ipsum.</h1>
          </div>
```

이런경우 아래와 같이 처리해준뒤 trim을 쓰면 깔끔하게 출력됩니다. 

> `trim()` 은 문자열 앞뒤에 불필요한 공백을 지워줍니다.

```javascript
function a () {
  return `
<div>
    <h1>Lorem ipsum.</h1>
</div>
  `.trim()
}
console.log(a())
console.log(a().replace(/\n/g, ''))
```

### template인 이유

근데 template이라는 말이 왜 붙었을지 궁금할 수 있습니다. 

혹시 handlebars , pug, jsp ,php , asp 를 들어보셨나요?  

바로 template engine들 입니다. 

template engine들은 html문서 안에 변수를 넣을수 있게 하거나, 조건등을 넣어줄 수 있습니다. 

template literal을 쓰면 아래와 같이 template engine과 같은 기능을 구현해 줄 수 있겠죠?

```javascript
const linesToHTML = function (characters) {
  return characters.reduce(function (charactersResult, character) {
    let {name, lines} = character
    return `${charactersResult || ''}
<article>
  <h1>${name}</h1>
  <ul>
    ${lines.reduce(function (linesResult, line) {
      return `${linesResult || ''}
    <li>${line}</li>
      `.trim()}, 0)}
  </ul>
</article>
  `.trim()}, 0)
}
const characters = [{
  name: 'Aria Stark',
  lines: ['A girl has no name.']
}, {
  name: 'John Snow',
  lines: [
    'You know nothing, John Snow.',
    'Winter is coming.'
  ]
}]
document.body.innerHTML = linesToHTML(characters)
```

## 번외 - forEach, map, reduce

프론트앤드 개발자로써 우리는 배열을 다룰 일이 매우 많기 때문에 이 3가지는 꼭 알고 넘어가야 합니다. 

또한 모든 인자들은 중요한 순서로 오기 때문에 JQuery와 다르게 햇갈릴 일이 적습니다.

### 1\) forEach

[MDN - Array.prototype.forEach](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach)

배열을 대상으로 for문을 편하게 돌릴 수 있습니다. 

`Array.prototype.forEach(callback[, thisArg])`

callback은 필수로 받아야 하며 thisArg는 있어도 없어도 됩니다.

* `callback`: `function (currentValue[, index[, originalArray]])`
  * `currentValue`: 현재값
  * `index`: 현재 인덱스
  * `originalArray`: 원본 배열
* `thisArg`: this에 할당할 대상. 생략시 global객체

```javascript
const a = [ 1, 2, 3 ]
a.forEach(function (v, i, arr) {
    console.log(v, i, arr, this)
}, [ 10, 11, 12 ])

=====결과=====
1 0 [1,2,3][10,11,12]
2 1 [1,2,3][10,11,12]
3 2 [1,2,3][10,11,12]
```

### 2\) map

[MDN - Array.prototype.map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)

for문을 돌려서 새로운 배열을 만드는 목적

`Array.prototype.map(callback[, thisArg])`

* `callback`: `function (currentValue[, index[, originalArray]])`
  * `currentValue`: 현재값
  * `index`: 현재 인덱스
  * `originalArray`: 원본 배열
* `thisArg`: this에 할당할 대상. 생략시 global객체

```javascript
const a = [ 1, 2, 3 ]
const b = a.map(function (v, i, arr) {
    console.log(v, i, arr, this)
    return this[0] + v
}, [ 10 ])
console.log(b);
===== 결과 =====
[11,12,13]
```

a라는 배열을 3번 돌면서 함수가 실행되어 새로운 배열을 만들어주었습니다.

그 함수는 this즉 \[10\]에 a배열 내의 값을 더해주는 함수입니다. 

새로운 배열을 만들어주기  때문에 항상 변수에 대입을 해주어야 합니다.

### 3\) reduce

[MDN - Array.prototype.reduce](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/map)

for문을 돌려서 최종적으로 다른 무언가를 만드는 목적

`Array.prototype.reduce(callback[, initialValue])`

* `initialValue`: 초기값. 생략시 첫번째 인자가 자동 지정되며,  

  이 경우 currentValue는 두번째 인자부터 배정된다.

* `callback`: `function (accumulator, currentValue[, currentIndex[, originalArray]])`
  * `accumulator`: 누적된 계산값
  * `currentValue`: 현재값
  * `currentIndex`: 현재 인덱스
  * `originalArray`: 원본 배열

```javascript
const arr = [ 1, 2, 3 ]
const res = arr.reduce(function (p, c, i, arr) {
  console.log(p, c, i, arr, this)
  return p + c
}, 10)
==========
10 1 0
11 2 1
13 3 2
res = 16
```

1. accumulator\(p\)에는 처음에 10이 들어갑니다. 
2. 10 + 1 =11 즉, 11이 다음 루프에 p 로 들어갑니다.
3. 반복..
4. 결과는 16

아래에서도 마찬가지 입니다. 여기서 주의깊게 봐야할 점은 initialValue즉, 기본값에 ' ' 이 들어가 있다는 점입니다. 공백 문자열이지만 들어가 있는 것이죠.

```javascript
const arr = [ 1, 2, 3, 4 ]
const str = arr.reduce(function (res, item, index, array) {
  return res + item
}, '')
console.log(str)
```

| step | res | item | index | array |
| :---: | :---: | :---: | :---: | :---: |
| 1 | '' | 1 | 0 | \[1,2,3,4\] |
| 2 | '1' | 2 | 1 | \[1,2,3,4\] |
| 3 | '12' | 3 | 2 | \[1,2,3,4\] |
| 4 | '123' | 4 | 3 | \[1,2,3,4\] |
| result | '1234' |  |  |  |

첫번째 순회때 accumulator에 기본값이 들어가서 순회를 돈것 보이시나요? 

그렇다면 기본값이 없는 예제입니다. 

```javascript
const arr = [ 'a', 'b', 'c', 'd' ]
const str = arr.reduce(function (res, item, index, array) {
  return res + item
})
console.log(str)
```

| step | res | item | index | array |
| :---: | :---: | :---: | :---: | :---: |
| 1 | 'a' | 'b' | 1 | \['a','b','c','d'\] |
| 2 | 'ab' | 'c' | 2 | \['a','b','c','d'\] |
| 3 | 'abc' | 'd' | 3 | \['a','b','c','d'\] |
| result | 'abcd' |  |  |  |

보이시나요? 기본값이 없을경우 첫번째 accumulator에는 배열의 0번 index값이 들어갑니다. 

또 객체로 만들어 낼 수도 있습니다. 

```javascript
const arr = [ 'a','b','c' ]
const r = arr.reduce(function (res,item,index,array) {
  res[item]=item;
  return res
},{})
console.log(r)
=======================
{a:'a',b:'b',c:'c'}
```

즉 accumulator를 뭐로 반환해 줄것이냐에 따라 여러가지로 만들어낼 수 있습니다. 

## 

## 4. template tag function

```javascript
//일반함수 
//매개변수를 받아 객체로 만들어준다.
const tag = function (strs, arg1, arg2) {
  return {strs: strs, args: [arg1, arg2]}
}
const res = tag `순서가 ${1}이렇게 ${2}`
console.log(res)
==========================
{
strs:["순서가 ","이렇게 ","",raw:Array(3)],
args:[1,2]
}
```

자, 만들어진 함수를template tag로 실행하면, 

1. 첫번째 인자 **strs**에는````````안에 문자열들이 배열로 들어간다.
2. 그 뒤 인자들은 보간들 `${}` 이 순서대로 들어간다.

실제로  **\( \)** 없이 함수를 **\` \`** 로 tag함수로써 호출한 것입니다.

아래 예제를 봐주세요. 

> ...expresions는 나머지 모든 인자들을 배열로 받아줍니다.

```javascript
const tags = function (strings, ...expressions) {
  console.log(strings, expressions)
}
const a = 'iu', b = 'Friday'
const str = tags `Hello, ${a}! Today is ${b}!!`
// ["Hello, ", "! Today is ", "!!"]
// ["iu", "Friday"]
```

{% hint style="info" %}
첫번째 인자 즉 문자열들이 넘겨진 배열의 length는 무조건 보간 즉, `${}` 의 수보다 하나 더 많을수밖에 없습니다. 
{% endhint %}

### 1\) expression의 수는 언제나 string의 수보다 하나 적다!

```javascript
const tags = function (strs, ...exps) {
  return { strs, exps }
}
console.log(tags `${10}${20}`)
// [10,20] , ['','','']
console.log(tags `a${30}`)
// [30] , ['a','']
console.log(tags `${40}b`)
// [40] , ['','b']
```

### 2\) 이를 이용하면 strings 또는 expressions 중 하나를 순회하여 별도의 처리가 가능하다.

아래 코드는 보간 즉 변수들은 건너 뛰고 문자열 끝마다 \_suffix \(띄어쓰기\) 를 추가해주는 것입니다.

```javascript
const addSuffix = function (strs, ...exps) {
  return strs.reduce(function (acc, curr, i) {
    let res = acc + curr + '_suffix '
    if(exps[i]) res += exps[i]
    return res
  }, '')
}
console.log(addSuffix `이 함수는${'각 문자열'}마다${'|_suffix|'}라는 글자를 추가합니다.`)
```

### 3\) examples

아래는 실무에서도 쓰기 좋은 1000단위마다 , 를 찍는 예제입니다. 

```javascript
const setDecimalSeperators = function (strs, ...args) {
  return args.reduce(function (p, c, i) {
    return p + strs[i] + (c + '').replace(/\d{1,3}(?=(\d{3})+(?!\d))/g, '$&,')
  }, '') + strs[strs.length - 1]
}
const res = setDecimalSeperators `이 사과는 하나에 ${2000}원이고, 총 ${1234567}개를 구입하시면 총 ${2000 * 1234567}원 이에요.`
console.log(res)
```

문자열들은 strs로 들어가고 나머지 보간들을 args배열로 받아서 그 args배열의 각 아이템들마다 , 를 정규표현식으로 찍어주었습니다.

Map은 새로나운 자료구조의 하나인데, 매번 셋팅하기가 어렵다는 단점이 있습니다. 

지금은 이해가 되지 않을수도 있으니, Map을 배우고 보시는걸 추천합니다.

```javascript
const createCollection = {
  Map(keys, ...vals){
    const m = new Map()
    vals.forEach(function (val, i) {
      m.set(keys[i].trim(), val)
    })
    return m
  },
  WMap(keys, ...vals){
    const wm = new WeakMap()
    for (let i = 0 ; i < vals.length ; i+=2) {
      wm.set(vals[i], vals[i+1])
    }
    return wm
  },
}

const wkeys = [ {a : 100}, {b : 200} ]
const map = createCollection.Map `
  a ${10}
  b ${'what'}
  fn ${v => v + 10}`
let wmap = createCollection.WMap `
  ${wkeys[0]} ${10}
  ${wkeys[1]} ${20}`
console.log(map)
console.log(wmap)
```

## 5. String.raw

처음에 보았던 코드입니다.

```javascript
const tag = function (strs, arg1, arg2) {
  return {strs: strs, args: [arg1, arg2]}
}
const res = tag `순서가 ${1}이렇게 ${2}`
console.log(res) 
```

![&#xCF58;&#xC194;&#xC5D0; &#xCD9C;&#xB825;&#xD55C; &#xACB0;&#xACFC;](.gitbook/assets/image.png)

근데 strs안에 raw라는 똑같은 배열이 들어있습니다. 

무엇을 하는 배열일까요? 

```javascript
const tag = function (strs, arg1, arg2) {
  return {strs: strs, args: [arg1, arg2]}
}
const res = tag `순서가 ${1}이렇게 ${2}\n\n`
console.log(res) 
```

![raw&#xAC00; &#xB2E4;&#xB974;&#xAC8C; &#xB098;&#xC640;&#xC788;&#xC8E0;? ](.gitbook/assets/image%20%281%29.png)

*  strs는 화면 그대로 위 결과에서처럼 줄바꿈 처리가 되있으면, 엔터두번 이라고 표시가 되는 반면,
* raw배열 안에서는 입력한 코드 그대로가 출력되었습니다.

raw는 입력한 것 그대로를 보여주는 배열입니다.



그럼 String.raw는 뭘까요? 

String이라는 생성자 함수에 포함되어있는 내장 메소드입니다. 

```javascript
console.log(`Hello\nWorld!`);
"Hello
World!"
=============================
console.log(String.raw`Hello\nWorld!`);
"Hello\nWorld!"
=============================
console.log(String.raw`Hello
World!`);
"Hello
World!"
```

