---
layout: post
title: "Javascript this"
date: 2016-08-01 00:00:00
author: 승구
categories: [javascript]
tags: [javascript, this]
cover: "/assets/black-background.png"
---

## 1. 항상 변화하는 this

Javascript 코드를 작성하다 보면 생각대로 잘 구현도 했는데 결과값이 이상하게 나올 때가 있습니다.
오늘 포스팅 주제인 `this` 때문에 그런 적도 한 두번이 아닌 것 같습니다.
그 만큼 좀 까다롭고 헷갈리는 javascript의 특징 중 하나입니다.
특히 동적 언어다 보니 디버깅도 쉽지 않아서 `this` 하나 잘못쓰다가 시간을 낭비하는 일이 허다합니다.
this가 헷갈리고 어렵게 느껴지는 이유가 코드의 문맥에 따라서 `this`의 값이 달리지기 때문인데요.
**하지만, 한번 정리하면 크게 어려울 것도 없습니다.**
<br><br>

## 2. Global Context

우선 제일 쉽고 간단한 `Global Context`의 this부터 보겠습니다.
Global context에선 아래 코드와 같이 **global object**을 가르키게 됩니다.

``` javascript

console.log(this === window); // true -web
console.log(this === global); // true -node

```
<br><br>

## 3. Function Context (non strict mode)

`Function Context` this는 그저 함수 내에서 사용되는 this입니다.
이제부터 조금 상황에 따라 다르게 this가 변하므로 정리가 필요합니다.
함수는 보통 대다수의 경우가 호출했을 때의 상황에 따라 this가 변하게 됩니다.
우선 전역에 함수가 설정되어 있고 그 함수내에 this가 있고 
전역에서 함수를 호출한다면 this는 global이 됩니다.
**(정확히 얘기하자면 아래에 나올 object member로써 쓰이지 않으면 참조할 객체가 없고 
'strict mode'가 아니기 때문에 항상 this는 어떤 객체든 참조해야 하므로 global를 가지는 것입니다)**

```javascript

function f1() {
	return this;
}

console.log(f1() === window); // true -web
console.log(f1() === global); // true -node

```
<br><br>

## 4. Function Context (strict mode)

`strict mode`에선 약간 다르게 동작합니다.
기본적으로 `strict mode`에선 this가 참조할 객체가 없으면 **undefined**로 나타나게 됩니다.
(아직 지원하지 않는 브라우저가 있을 수 있습니다)

```javascript

'use strict';

function f1() {
	return this;
}

console.log(f1()); // undefined

```
<br><br>

## 5. Arrow function

ES6부터 function를 좀 더 표현하기 쉽게된 `Arrow function`이 추가되었는데요.
`Arrow function`안의 this은 위의 기존 함수들안의 this와는 조금 다르게 동작합니다.
기존 함수는 함수 호출에 따라 this가 달라지지만 `Arrow function`은 정의할 때의 
**가장 근접한 context의 this**를 사용하고 언제 어떻게 호출하든 정의할 때의 this로 고정되어 사용됩니다.
아래 코드처럼 `call, apply, bind`를 통해 강제로 this를 변경하여도 그대로 고정된 값이 사용되고,
`object에 member`로 호출하여도 this는 해당 object가 아닌 처음 정의 될때의 global로 적용됩니다.
**(object member나 call, apply, bind는 아래에 설명되어 있으니 이해가 안되시더라도 넘어가면 됩니다)**

```javascript

'use strict';

var global = this;
var arrowFunc = (() => this);
console.log(arrowFunc() === global); // true

// object member
var obj = { foo: foo };
console.log(obj.foo() === global); // true

// call, apply
console.log(foo.call(obj) === global); // true
console.log(foo.apply(obj) === global); // true

// bind
foo = foo.bind(obj);
console.log(foo() === global); // true

```
<br><br>

## 6. object member

`객체의 멤버`로 함수가 사용되면 호출 시점에서 this는 해당 함수를 **멤버로 가지고 있는 객체**로 지정되게 됩니다.
주의해야 할 점은 `객체의 멤버`로 호출될 때는 this가 위처럼 객체가 되지만,
그 객체 멤버 함수를 다른 변수로 대입하여 다르게 호출 될 땐 대입된 함수의 호출시점에 따라 달라지게 됩니다.
아래 코드를 보며 정리하시길 바랍니다
(아래 코드에선 fn은 Global Context에서 사용되었고 strict mode이므로 undefined!)
(이해가 안되시면 Function Context를 다시 보시길 바랍니다)

```javascript

'use strict';

var obj = {
  foo: function() {
    return this;
  }
};

var fn = obj.foo;

console.log(obj.foo() === obj); // true
console.log(fn() === obj); // false
console.log(fn()); // undefined

```

그리고 주의 할 점은 `Prototype Chaining`을 할 때인데, 생각보다 간단합니다.
부모 Prototype에 정의된 함수의 this를 사용할 땐, 아래 코드처럼 마치 자신의 멤버함수처럼 this는 **호출한 객체**가 됩니다. 

```javascript

'use strict';

var parentObj = {
	a: 'parentA',
	b: 'parentB',
	printAB: function() {
    	return this.a + this.b;
  	}
};

console.log(parentObj.printAB); // parentAparentB

// parentObj를 prototype 상속하는 childObj 만듬
var childObj = Object.create(parentObj);
childObj.a = 'childA';
childObj.b = 'childB';

console.log(childObj.printAB()); // childAchildB

```
<br><br>

## 7. Contructor

`생성자 함수`로 new 연산자를 사용하여 객체를 만드는데요.
`생성자 함수`내에서 사용되는 this는 생성자 함수와 new 연산자의 조합으로 만들어진 객체를 나타냅니다.
즉 this는 객체가 만들어지고 **초기화될 객체**입니다.

```javascript

function Person() {
	// 생성될 객체에 변수 초기화
	this.firstName = 'Seunggu',
	this.lastName = 'Jeong',
	
	// 보통 prototype에 함수를 넣지만 편의상 이렇게 표현 했습니다.
	this.printName = function() {
		return this.lastName + ' ' + this.firstName;
	}
}

var seunggu = new Person();
console.log(seunggu.printName()); // 'Jeong Seunggu'

```
<br><br>

## 8. review, example

```javascript

'use strict';

var obj = {
	foo: function() {
		return function() {
			return this;
		}
	},

	fooArrow: function() {
		return (() => this)
	}
};

// 시나리오 #1
console.log(obj.foo()()); // undefined
console.log(obj.foo()() === obj); // false
console.log(obj.fooArrow()() === obj); // true

// 시나리오 #2
var f1 = obj.foo;
var f2 = obj.fooArrow;

console.log(f1()()); // undefined
console.log(f1()() === obj); // false
console.log(f2()()); // undefined
console.log(f2()() === obj); // false

```

우선 obj는 멤버로 함수를 가지고 있고, 그 함수는 this를 리턴하는 함수를 리턴한다.
`시나리오 #1`의 obj.foo부터 보면 obj.foo가 실행되면 안에 foo 함수가 실행되면서 this를 리턴하는 함수가 반환되는데,
어떤 객체의 멤버로 함수가 호출된 것이 아니므로 this는 **undefined**가 된다.
다음은 obj.fooArray가 실행되면서 this를 리턴하는 Arrow function이 반환되는데,
Arrow function은 가장 인접한 context의 this를 가져오므로 fooArrow context의 this를 가져온다.
결국 this는 **obj**가 된다.

`시나리오 #2`에선 우선 obj에 있는 멤버 함수들을 모두 전역의 변수로 대입하여 `시나리오 #1`와 같이 코드를 돌려보면,
obj.foo의 this는 `시나리오 #1`과 같은 이유로 **undefined**가 되지만, obj.fooArrow의 this는 `시나리오 #1`과 결과가 같지 않습니다.
헷갈릴 수도 있지만 천천히 분석해 보시면 **당연한 결과**입니다.
우선 obj.fooArray를 f2 변수로 대입했고 함수는 호출에 따라 this가 변하므로 
f2가 호출될 땐 객체의 멤버 변수가 아니므로 당연히 **undefined**가 됩니다.
그리고 Arrow function은 가장 가까운 context의 this를 가져오게 되므로 **undefined**를 가져오게 됩니다.

## 9. 마지막으로, call, apply, bind

this를 완벽히 마스터하기 위해선 `call, apply, bind`까지 이해해야 합니다.
이 세 함수의 역할은 this를 **명시적으로 변경해주는 역할**입니다.
세 함수 모두 **Function Prototype**에 존재합니다.
예제를 통해 확인해보겠습니다.

```javascript

'use strict';

var preObj = {
	name: 'preObj',
	printAB: function(a, b) {
		return this.name + ' ' + a + ' ' + b;
	}
};

var afterObj = {
	name: 'afterObj'
};

// call, apply를 이용한 this 대입
console.log(preObj.printAB('A', 'B')); // preObj A B
console.log(preObj.printAB.call(afterObj, 'A', 'B')); // afterObj A B
console.log(preObj.printAB.apply(afterObj, ['A', 'B'])); // afterObj A B

// bind를 이용한 this 대입
var fn = preObj.printAB.bind(afterObj);
console.log(fn('A', 'B')); // afterObj A B

```

`call, apply`를 보시면 첫번째 인자에 this에 대입할 객체를 넣고, 
두번째 파라미터부터 this를 대입하는 함수의 인자들을 차례대로 넣으면 됩니다.
`call과 apply`의 차이는 인자를 각각 넣느냐 배열로 넣으냐의 차이입니다.

`bind`는 조금 다른 개념인게 함수를 사용할 때 사용한단 느낌 보단
함수를 미리 원하는 객체로 this를 바인딩 해놓는 개념입니다.
코드처럼 `bind`를 이용하여 afterObj를 this로 바인딩된 함수를 반환합니다.
그 함수를 통해 기존의 함수처럼 사용할 수 있습니다.
<br><br>

## 10. 정리

지금까지 this의 다양한 상황에 따른 변화와 this를 수동으로 대체하는 것까지 모두 보았습니다.
이렇게 한번 this를 정리하면 언제 this가 나오더라도 편하게 분석이 가능해서 좋습니다.
영어로 된 문서지만 **참고자료**를 통해 한번 읽으셔도 좋을 것 같아 밑에 리스팅해 놓았습니다.
<br><br>

## 11. 참고자료

* [MDN javascript this](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/this)
* [Understand JavaScript’s “this” With Clarity, and Master It](http://javascriptissexy.com/understand-javascripts-this-with-clarity-and-master-it/)




