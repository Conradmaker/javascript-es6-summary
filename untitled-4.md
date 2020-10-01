---
description: 6.Spread operator
---

# 6.Spread operator

## Spread operator

Spread 연산자 즉, 펼치기 연산자는 무슨 기능을 할까요? 

전개 연산자라고도 부르기도 합니다. 

## 1. 소개

```javascript
var birds = ['eagle', 'pigeon']
var mammals = ['rabbit', 'cat']
var animals = birds.concat('whale').concat(mammals)
console.log(animals)
//['eagle', 'pigeon', 'whale' ,'rabbit', 'cat']

const animals2 = [...birds, 'whale', ...mammals]
console.log(animals2)
//['eagle', 'pigeon', 'whale' ,'rabbit', 'cat']
```

끝입니다. 코드만 봐도 이해가 가능하죠?

## 6-2. 상세

### 1\) 배열의 각 인자를 펼친 효과

아래 코드에서 Math.max는 원래 인자를 배열로 받는게 아닌 각각 받아 최고값을 반환해줍니다.

```javascript
const values = [20, 10, 30, 50, 40]
console.log(20, 10, 30, 50, 40) //20,10,30,50,40
console.log(...values)          //20,10,30,50,40

console.log(Math.max(20, 10, 30, 50, 40))
//과거에는 apply사용 -> 두번째 인자로 배열을 받기 때문에
console.log(Math.max.apply(null, values))
//이제 spread operator 사용
console.log(Math.max(...values))
```

### 2\) 앞뒤로 다른 값들을 함께 사용할 수도 있다.

rest parameter는 항상 마지막에만 사용할 수 있었습니다.

하지만, spread operator는 받는게 아닌 주는 입장이기 때문에 어느위치에도 사용이 가능합니다.

```javascript
const values = [3, 4, 5, 6, 7, 8]
const sum = function (...args) {
  return args.reduce(function (p, c) {
    return p + c
  })
}
console.log(sum(1, 2, ...values, 9, 10))
```

### 3\) iterable한 모든 데이터는 펼칠 수 있다.

iterable은 반복할 수 있는 이라는 뜻을 가지고 있습니다. 

배열처럼 index가 있고, index를 순회하며 값을 찾아낼수 있는 값을 말합니다. 

```javascript
const str = 'Hello!'

const splitArr = str.split('') //['H','e','l','l','o','!']
const restArr = [...str]       //['H','e','l','l','o','!']
```

당장은 펼칠수 있을 것 같은면 다 펼칠수 있다고 생각하시면 될 것 같습니다. 

### 4\) push, unshift, concat 등의 기능을 대체할 수 있다.

```javascript
let originalArr = [2, 3]
const preArr    = [-2, -1]
const sufArr    = [6, 7]

originalArr.unshift(1) //[1,2,3]
originalArr.push(4)    //[1,2,3,4]
originalArr = [0, ...originalArr, 5]  //[0,1,2,3,4,5]
console.log(originalArr)

const concatArr = preArr.concat(originalArr, sufArr)
const restArr = [...preArr, ...originalArr, ...sufArr]
console.log(concatArr, restArr)
```

5~7번 line을 보면 unshift와 push는 기존 배열에 추가를 하는 것이지만, spread operator는 새로운 배열을 만들어 냅니다. 변수에 새로 대입을 안해주었기 때문에 주소값이 바뀌겠죠.

그리고 기존에 있던 배열의 주소를 가리키던 originalArr의 가리키는 주소값이 달라졌기 때문에 GC가 처리해주겠죠! 

{% hint style="info" %}
특히 unshift같은 경우는 원래 있던 배열의 index를 한깐씩 밀어내고, 값을 0번 index에 넣기 때문에 비용이 상당히 많이 들어갑니다. \(push는 뒤에 추가이기 때문에 상관 X\)

차라리 spread operator로 새로운 배열로 만들어주는 것이 더 효과적이라고 생각합니다.
{% endhint %}

### 5\) '새로운' 배열이다.

바로 위에서 살펴본 내용입니다. 

```javascript
let originalArray = [1, 2]
let copiedArray = [...originalArray]
console.log(originalArray === copiedArray)

originalArray.push(3)
console.log(originalArray)
console.log(copiedArray)
```

### 6\) '얕은복사'만을 수행한다.

```javascript
let originalArray = [{
  first: 'Hello,',
  second: 'World!'
}, {
  first: 'Welcome',
  second: 'ES6!'
}]
let copiedArray = [...originalArray]
console.log(originalArray[0].first)  //Hello

copiedArray[0].first = "Hi,"
console.log(originalArray[0].first)  //Hi
```

얕은 복사를 하기 위해 불변성을 지켜주기 위해서는 각각의 객체를 찾아가면서 객체 안에있는 property를 찾아와서 모두 복사를 해주어야만 깊은 복사가 됩니다.

> ## 참고
>
> * [tc39 propersals](https://github.com/tc39/proposals)
>   * tc39 는 ECMA라는 표준기구에서 39번째 위원회입니다. 이 위원회에서는 JS에 대한 표준 규약을 제정합니다!!
>   * stage0: 전세계사람들에게 '이기능 추가좀!!' 제안을 받아
>   * stage1: 검토
>   * stage2: 구체화
>   * stage3: 도입 예정
>   * stage4: 도입 확정
> * [object-rest-spread](https://github.com/tc39/proposal-object-rest-spread)

