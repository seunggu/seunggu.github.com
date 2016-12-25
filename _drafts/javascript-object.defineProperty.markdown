---
layout: post
title: "Javascript Object.defineProperty()"
date: 2016-12-24 14:09:03
author: 승구
categories: [javascript]
tags: [javascript, Object, defineProperty]
cover: "/assets/black-background.png"
---

## 1. Object.defineProperty

```javascript
var obj = {};
obj.prop = 1;
```

간단히 말하자면, 위의 코드처럼 obj객체에 prop 프로퍼티를 생성하는 것과 같습니다. 하지만 `defineProperty`를 사용하면 **프로퍼티를 생성할 때 해당 프로퍼티에 대한 더욱 강력한 설정을 할 수 있습니다.** 이런 설정들은 ES5로 넘어오면서 다른 정적언어(c++, java 등)들이 지원하는 접근 지시자나 게터 세터를 자바스크립트에서 더욱 명시적으로 사용하기 위해 탄생했다는 생각입니다. 이제 어떠한 강력한 설정들이 우리에게 편의를 줄 수 있는지 보도록 하겠습니다.
<br/><br/>

## 2. Syntax

```javascript
var obj = {};

Object.defineProperty(obj, 'name', {
	enumerable: true,
	configurable: false,
	writable: true,
	value: 'seunggu'
})
```

우선 문법부터 살펴 보도록 하겠습니다. 위의 코드를 보면 `Object.defineProperty`를 사용하여서 name 프로퍼티를 생성하였는데요. 함수의 세 번째 parameter를 보면 각종 프로퍼티를 담은 객체가 있는 것 볼 수 있습니다. **이 객체를 descriptor라고 하고 프로퍼티에 대한 설정을 명시하는 곳이라고 생각하시면 됩니다.**
<br><br>

## 3. descriptor

2번 섹션에서 보듯이, 이 `descriptor`가 핵심적인 역할을 한다고 볼 수 있습니다. `descriptor`는 두 개의 종류(**data descriptor, access descriptor**)가 있습니다. `data descriptor`는 해당 프로퍼티가 값을 가지는 데이터 프로퍼티라고 생각하시면 되고 `access descriptor`는 getter와 setter를 사용하여 특정 값을 이용하여 값을 가져오는 것이라고 생각하시면 됩니다.
<br/><br/>

## 4. descriptor\`s property

2번 섹션의 코드처럼 `descriptor 객체`에는 여러가지 프로퍼티가 있었습니다. 이런 프로퍼티들이 설정에 대한 역할을 하게 되는데, **descriptor의 종류에 따라 설정하는 프로퍼티가 다릅니다.** 두 종류의 descriptor 모두 가지게 되는 공통 설정 프로퍼티(configurable, enumerable)가 2개 있고, descriptor에 종류에 따라 각각 다르게 가지는 설정 프로퍼티가 2개가 있습니다.
<br/><br/>

## 5. 공통 property

위에서 언급했듯이 공통 프로퍼티가 `configurable`과 `enumerable` 두 개가 있습니다. `configurable`은 값이 `true`일 경우, defineProperty 메서드의 두 번째 parameter 즉 객체에 생성하는 프로퍼티에 대해서 다른 descriptor를 다시 설정 할 수 있게하고 생성된 프로퍼티의 삭제가 가능하게 합니다. 그리고 `false`일 경우엔 다른 descriptor를 다시 설정할 경우 TypeError를 Throw하고 delete를 이용하여 프로퍼티를 삭제할 경우에도 아무일도 일어나지 않게 합니다.
<br><br>
`enumerable`은 for...in이나 Object.key()처럼 해당 객체에 대해 property enumeration이 일어날 때 설정하는 프로퍼티를 보여줄지 아닐지에 대해 설정하는 겁니다. `true` 일 때엔 보이게 되고, `false`일 때엔 보이지 않게 됩니다. **두 개의 공통 프로퍼티 모두 default 값은 false 입니다.**

``` javascript
// 예시
var obj = {};
obj.a = 3;

Object.defineProperty(obj, 'name', {
	// 굳이 두 공통 프로퍼티를 명시하지 않아도 default는 false
	configurable: false,
	enumerable: false
});

// enumerable를 false로 한 name은 나오지 않지만 a는 나오게 됨
// 결과: a
for (var key in obj) {
	console.log(key);
}

// name에 대해서 다시 descriptor를 설정하려하면 TypeError 발생!
Object.defineProperty(obj, 'name', {
	configurable: true,
	enumerable: true
});
```

``` javascript
// 예시
var obj = {};
obj.a = 3;

Object.defineProperty(obj, 'name', {
	// 굳이 두 공통 프로퍼티를 명시하지 않아도 default는 false
	configurable: true,
	enumerable: true
});

// enumerable가 true 이므로 a, name 모두 나오게됨
// 결과: a name
for (var key in obj) {
	console.log(key);
}

// configurable이 true이므로 descriptor 재정의 가능
Object.defineProperty(obj, 'name', {
	configurable: false,
	enumerable: false
});
```
<br/><br/>

## 6. data descriptor의 property

위의 언급처럼 descriptor는 `data descriptor`와 `access descriptor` 두 가지 종류가 있습니다. `data descriptor`는 **생성되는 프로퍼티가 데이터 값을 가지게 되는 것이라 생각하시면 됩니다.** descriptor내의 설정 프로퍼티는 `value`와 `writable`을 가지게 됩니다. `value`는 생성되는 프로퍼티가 가지는 값이라고 편하게 생각하면 됩니다. 값은 자바스크립트에서 제공하는 값 어느 것이든 대입할 수 있습니다. `writable`은 그 값을 변경할 수 있는지 없는지에 대한 설정입니다.
밑의 예시를 보며 더 자세히 이해 해보도록 하겠습니다.

```javascript
var obj = {};

Object.defineProperty(obj, 'name', {
	// 굳이 두 공통 프로퍼티를 명시하지 않아도 default는 false
	configurable: true,
	enumerable: true,
	value: 'seunggu',
	writable: false
});

console.log(obj.name); // 'seunggu'
obj.name = 'any'; // 값이 바뀔 수 없지만 에러는 나진 않습니다.(아무일도 일어나지 않음)
console.log(obj.name); // 'seunggu'
```
<br/><br/>

## 7. access descriptor의 property

나머지 하나의 descriptor는 `access descriptor` 입니다. `access descriptor`를 이용하여 생성된 프로퍼티는 data descriptor를 이용한 프로퍼티처럼 값을 본인이 가지는 것보단 **다른 값을 이용하거나 여러 값을 이용하여 계산된 값을 가져오는 getter, setter 기반을 이용하게 됩니다.** descriptor 내의 설정 프로퍼티는 get과 set을 가지게 됩니다. get과 set 모두 다른 언어에서의 getter와 setter와 같다고 생각하시면 됩니다. 아래의 예제를 보며 더 자세히 알아보겠습니다.

```javascript
var obj = {};
var nameStr = 'seunggu';

Object.defineProperty(obj, 'name', {
	// 굳이 두 공통 프로퍼티를 명시하지 않아도 default는 false
	configurable: true,
	enumerable: true,
	get: function() {
		return 'My name is ' + nameStr;
	},
	set: function(newValue) {
		nameStr = newValue;
	}
});

console.log(obj.name); // 'My name is seunggu'
obj.name = 'any';
console.log(obj.name); // 'My name is any'
console.log(nameStr); // 'any'
```
<br/><br/>

## 8. 정리

지금까지 `defineProperty` 메서드에 대해서 알아보았습니다. 결국 이 메서드는 객체에 어떤 프로퍼티를 생성할 때 해당 프로퍼티에 대한 설정을 descriptor를 이용하여 한 것을 볼 수 있었고, 설정을 하기위해 어떤 descriptor와 어떤 설정 프로퍼티를 이용해야 하는지 알아보았습니다. 이런 설정을 함으로써 기존의 프로퍼티 생성보다 더욱 자바스크립트를 강력하고 코드의 실수를 줄이는 방향으로 만들어 줍니다. 마지막으로 `defineProperty` 메서드를 이용하여 descriptor에 아무런 설정 프로퍼티를 입력하지 않으면 모두 default가 false 또는 undefined 이지만, 아래 코드처럼 프로퍼티를 생성할 경우엔 descriptor는 data descriptor이며 설정 프로퍼티는 모두 true를 가지게 됩니다.

```javascript
var obj = {};

o.name = 'seunggu';

// 위의 코드는 아래의 코드와 같습니다.
Object.defineProperty(o, 'name', {
  value: 'seunggu',
  writable: true,
  configurable: true,
  enumerable: true
});
```
<br/><br/>

## 9. 참고자료

* [MDN Object.defineProperty()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty)
