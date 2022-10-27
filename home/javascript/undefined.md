---
description: 기존과 달라진 ES6 에서의 순회와 이터러블 / 이터레이터 프로토콜
---

# 이터러블 / 이터레이터 프로토콜



<figure><img src="../../.gitbook/assets/스크린샷 2022-10-25 오후 5.29.34.png" alt=""><figcaption><p>이터레이션 프로토콜</p></figcaption></figure>

### 이터레이션 프로토콜이 왜 필요한가?

es6에서 부터 달라진 순회\
데이터 소비자 (Data consumer) 인 for ...of 문, spread 문법 등은 \
이터레이션 프로토콜을 준수하는 이터러블에서 사용이 가능하다.\
즉 이터러블은 (Data Provider) 의 역할을 한다.\
즉 이터레이션 프로토콜은 다양한 데이터 소스가 하나의 순회 방식을 가지도록 규정하여, \
소비자가 효율적으로 다양한 데이터 소스를 사용할 수 있도록 \
데이터 소비자와 데이터 소스를 연결하는 인터페이스의 역할을 한다.

<figure><img src="../../.gitbook/assets/스크린샷 2022-10-27 오후 3.35.53.png" alt=""><figcaption><p>이터러블은 데이터 소비자와 데이터 소스를 연결하는 인터페이스</p></figcaption></figure>

ES6 에서 제공하는 '빌트인 이터러블'

> Array, String, Map, Set, TypedArray(Int8Array, Uint8Array, Uint8ClampedArray, Int16Array, Uint16Array, Int32Array, Uint32Array, Float32Array, Float64Array),\
> DOM data structure(NodeList, HTMLCollection), Arguments
>
>



### 이터러블 (iterable)

> 이터레이터를 리턴하는 \[Symbol.iterator]\() 를 가진 값

이터러블 프로토콜을 준수한 객체를 이터러블이라고 부른다.\
이터러블은 Symbol.iterator 메서드를 구현하거나, \
또는 프로토타입 체인에 의해 상속한 객체를 말한다. \
예를 들어 배열은 Symbol.iterator 메서드를 Array.prototype 에 의해 상속받아\
소유하고 있기 때문에, 이터러블 프로토콜을 준수하는 이터러블이다.\




### 이터레이터

> { value, done } 객체를 리턴하는 next() 를 가진 값

이터레이터 프로토콜은 next 메소드를 소유하며 \
next 메소드를 호출하면 이터러블을 순회하며 \
value, done 프로퍼티를 갖는 이터레이터 리절트 객체를 반환하는 것이다. \
이 규약을 준수한 객체가 이터레이터이다.

이터러블 프로토콜을 준수한 이터러블은 Symbol.iterator 메소드를 소유한다. \
이 메소드를 호출하면 이터레이터를 반환한다. \
이터레이터 프로토콜을 준수한 이터레이터는 **next 메소드**를 갖는다.

```javascript
// 배열은 이터러블 프로토콜을 준수한 이터러블이다.
const array = [1, 2, 3];

// Symbol.iterator 메소드는 이터레이터를 반환한다.
const iterator = array[Symbol.iterator]();

// 이터레이터 프로토콜을 준수한 이터레이터는 next 메소드를 갖는다.
console.log('next' in iterator); // true
```

****\
****이터레이터의 next 메소드를 호출하면 \
value, done 프로퍼티를 갖는 **이터레이터 리절트(iterator result) 객체를 반환한다.**

```javascript
// 배열은 이터러블 프로토콜을 준수한 이터러블이다.
const array = [1, 2, 3];

// Symbol.iterator 메소드는 이터레이터를 반환한다.
const iterator = array[Symbol.iterator]();

// 이터레이터 프로토콜을 준수한 이터레이터는 next 메소드를 갖는다.
console.log('next' in iterator); // true

// 이터레이터의 next 메소드를 호출하면 value, done 프로퍼티를 갖는 이터레이터 리절트 객체를 반환한다.
let iteratorResult = iterator.next();
console.log(iteratorResult); // {value: 1, done: false}
```



이터레이터의 next 메소드는 이터러블의 각 요소를 순회하기 위한 포인터의 역할한다. \
next 메소드를 호출하면 이터러블을 순차적으로 한 단계씩 순회하며 이터레이터 리절트 객체를 반환한다.

```javascript
// 배열은 이터러블 프로토콜을 준수한 이터러블이다.
const array = [1, 2, 3];

// Symbol.iterator 메소드는 이터레이터를 반환한다.
const iterator = array[Symbol.iterator]();

// 이터레이터 프로토콜을 준수한 이터레이터는 next 메소드를 갖는다.
console.log('next' in iterator); // true

// 이터레이터의 next 메소드를 호출하면 value, done 프로퍼티를 갖는 이터레이터 리절트 객체를 반환한다.
// next 메소드를 호출할 때 마다 이터러블을 순회하며 이터레이터 리절트 객체를 반환한다.
console.log(iterator.next()); // {value: 1, done: false}
console.log(iterator.next()); // {value: 2, done: false}
console.log(iterator.next()); // {value: 3, done: false}
console.log(iterator.next()); // {value: undefined, done: true}
```

이터레이터의 next 메소드가 반환하는 이터레이터 리절트 객체의 value 프로퍼티는 \
현재 순회 중인 이터러블의 값을 반환하고 done 프로퍼티는 이터러블의 순회 완료 여부를 반환한다.





### 이터레이션 프로토콜 (iteration protocol)

데이터 컬렉션을 순회하기 위한 프로토콜(미리 약속된 규칙) 이다.\
이터레이션 프로토콜을 준수하는 객체는 for ..of 문으로 순회할 수 있고, \
Spread 문법의 피연산자가 될 수 있다.

```javascript
const array = [1, 2, 3];

// 배열은 Symbol.iterator 메소드를 소유한다.
// 따라서 배열은 이터러블 프로토콜을 준수한 이터러블이다.
console.log(Symbol.iterator in array); // true

// 이터러블 프로토콜을 준수한 배열은 for...of 문에서 순회 가능하다.
for (const item of array) {
  console.log(item);
}
```

일반 객체는 Symbol.iterator 메소드를 소유하지 않는다. \
따라서 일반 객체는 이터러블 프로토콜을 준수한 이터러블이 아니다.



```javascript
const obj = { a: 1, b: 2 };

// 일반 객체는 Symbol.iterator 메소드를 소유하지 않는다.
// 따라서 일반 객체는 이터러블 프로토콜을 준수한 이터러블이 아니다.
console.log(Symbol.iterator in obj); // false

// 이터러블이 아닌 일반 객체는 for...of 문에서 순회할 수 없다.
// TypeError: obj is not iterable
for (const p of obj) {
  console.log(p);
}
```

일반 객체는 이터레이션 프로토콜을 준수하지 않기 때문에 이터러블이 아니다. \
\[Symbol.iterator]\() 가 일반 객체는 소유하지 않는다.\
따라서 일반 객체는 for…of 문에서 순회할 수 없으며 \
Spread 문법의 대상으로 사용할 수도 없다.  \
(es9 부터는 사용이 가능함. 객체 내부에 전개할 수 있도록)\


```javascript
const obj = { a: 1, b: 2};

console.log(...obj) // 이렇게 작성할 수 없음. Symbol.iterator 메서드를 소유하고 있지 않기 때문에

const newObj = {...obj} // 그러나 es9 부터는 사용이 가능함

console.log(newObj); // { a: 1, b: 2}
```



<figure><img src="../../.gitbook/assets/스크린샷 2022-10-25 오후 5.26.10.png" alt=""><figcaption><p>이터러블 / 이터레이터 프로토콜 동작 원리</p></figcaption></figure>

### 사용자 정의 이터러블 만들어보기

```javascript
const iterable = {
    [Symbol.iterator]() {
      let i = 3;
      return {
        next() {
          return i == 0 ? {done: true} : {value: i--, done: false};
        },
        [Symbol.iterator]() {
          return this;
        }
      }
    }
  };
  let iterator = iterable[Symbol.iterator]();
  iterator.next();
  iterator.next();
  // log(iterator.next());
  // log(iterator.next());
  // log(iterator.next());
```





## for ...of 문

for…of 문은 내부적으로 이터레이터의 next 메소드를 호출하여 \
이터러블을 순회하며 next 메소드가 반환한 \
이터레이터 리절트 객체의 value 프로퍼티 값을 for…of 문의 변수에 할당한다. \
그리고 이터레이터 리절트 객체의 done 프로퍼티 값이 false이면 \
이터러블의 순회를 계속하고 true이면 이터러블의 순회를 중단한다.

```javascript
// 배열
for (const item of ['a', 'b', 'c']) {
  console.log(item);
}

// 문자열
for (const letter of 'abc') {
  console.log(letter);
}

// Map
for (const [key, value] of new Map([['a', '1'], ['b', '2'], ['c', '3']])) {
  console.log(`key : ${key} value : ${value}`); // key : a value : 1 ...
}

// Set
for (const val of new Set([1, 2, 3])) {
  console.log(val);
}
```



for ...of 문의 내부적인 동작 원리

```javascript
// 이터러블
const iterable = [1, 2, 3];

// 이터레이터
const iterator = iterable[Symbol.iterator]();

for (;;) {
  // 이터레이터의 next 메소드를 호출하여 이터러블을 순회한다.
  const res = iterator.next();

  // next 메소드가 반환하는 이터레이터 리절트 객체의 done 프로퍼티가 true가 될 때까지 반복한다.
  if (res.done) break;

  console.log(res);
}
```







### 이터러블과 유사 배열 객체

유사 배열 객체는 마치 배열처럼 인덱스로 프로퍼티 값에 접근할 수 있고,\
length 프로퍼티를 갖는 객체를 말한다. \
유사 배열 객체는 length 프로퍼티를 가지기 때문에,\
for 문으로 순회할 수 있고, 인덱스를 나타내는 숫자 형식의 문자열을 프로퍼티 로 가지므로,\
마치 배열처럼 인덱스로 프로퍼티 값에 접근할 수 있다.



```javascript
// 유사 배열 객체
const arrayLike = {
  0: 1,
  1: 2,
  2: 3, 
  length: 3
}

// 유사 배열 객체는 length 프로퍼티를 가지기 떄문에 for 문으로 순회할 수 있다.
for (let i = 0; i < arrayLike.length; i++) {
  // 유사 배열 객체는 마치 배열처럼 인덱스로 프로퍼티 값에 접근할 수 있다
  console.log(arrayLike[i]); // 1 2 3
}
```



유사 배열 객체는 이터러블이 아닌 일반 객체다.\
따라서 유사 배열 객체에는 Symbol.iterator 메서드가 없기 때문에, for ... of 문으로 순회할 수 없다.

```javascript
// 유사 배열 객체는 이터러블이 아니기 때문에 for ... of 문으로 순회할 수 없다.
for ( const item of arrayLike ) {
  console.log(item); // 1 2 3 
}

// -> TypeError: arrayLike is not iterable
```

단, arguments(함수의 매개변수 객체), NodeList, HTMLCollection, String \
은 유사 배열 객체이면서 이터러블이다.\
정확히 말하면 ES6 에서 이터러블이 도입되면서,\
유사 배열 객체인 arguments, NodeList, HTMLCollection 객체, String 에 Symbol.iterator 메서드를 구현하여\
이터러블이 되었다. \
하지만 이터러블이 된 이후에도 length 프로퍼티를 가지며 \
인덱스로 접근할 수 있는 것에는 변함이 없으므로 유사 배열 객체이면서 이터러블이다.\
배열도 마찬가지로 ES6 에서 이터러블이 도입되면서 Symbol.iterator 메서드를 구현하여 이터러블이 되었다.\
문자열은 원시형 데이터이지만, 객체처럼 접근하여 length 프로퍼티를 가질 수 있다.\
원시형 데이터를 객체처럼 사용할 수 있도록 임시 변환하는 작업을 박싱(Boxing) 이라 하는데,\
이렇게 변환된 문자열 객체도 유사 배열 객체이기 때문에 순회가 가능하다.



string 의 prototype 을 확인해보면 Symbol.iterator() 가 존재한다.

```javascript
const string = "asdf";
string.length // 4
console.log(Object.getPrototypeOf(string));
```

<figure><img src="../../.gitbook/assets/스크린샷 2022-10-27 오후 8.25.06.png" alt=""><figcaption></figcaption></figure>



> 참조 \
> [https://dev.to/capscode/what-is-array-like-object-in-javascript-3f5m](https://dev.to/capscode/what-is-array-like-object-in-javascript-3f5m)\
> [https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Iteration\_protocols](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Iteration\_protocols)



> Written by

{% embed url="https://github.com/jukangpark" %}

