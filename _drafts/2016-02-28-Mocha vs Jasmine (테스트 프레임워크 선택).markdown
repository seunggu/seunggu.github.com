---
layout: post
title:  "Mocha vs Jasmine(테스트 프레임워크 선택)"
date:   2016-02-29 00:00:00
author: 승구
categories: Javascript
tags:	단위 테스트, 테스트 프레임워크, Mocha, Jasmine, Jest, Guiter, Javascript
cover:  "/assets/black-background.png"
---

Javascript 프로젝트를 진행하시면 품질 좋은 코드를 보증하기 위해 단위 테스트는 당연시 됩니다.
아직 자신에게 맞는 테스트 프레임워크를 못 찾으신 분도 있으실테고,
이미 사용하시는 테스트 프레임워크가 있으실 수도 있습니다.

이번에 포스트에서는 현재 날짜(2016. 02. 29) 기준으로 테스트 프레임워크를 비교를 해볼까합니다.
사실상 Mocha vs Jasmine 이지만, 최근에 주목받고 있거나 특이한 프레임워크도 한번 소개 해볼까 합니다.\\
(테스트 프레임워크가 하는 기능들을 열거하는 것보단 비교해서 선택할 때 도움이 될 수 있도록 글을 간략히 했습니다)
<br><br>


## Mocha
[Express][Express]와 [Jade][Jade] 등을 만든 TJ 할로웨이 척이 만든 테스트 프레임워크인데, 현재 가장 인기있는 프레임워크입니다.
현재 Airbnb에서는 Mocha를 테스트 프레임워크로 쓰고 있고 밑의 사진은 `Mocha 프로젝트의 Github Graph`와 `Jasmine 프로젝트의 Github Graph`인데
사진을 보게되면 Jasmine보다 Mocha가 좀 더 개발이 활발하게 이루어 지고 있습니다.

##### Mocha Github Graph
<a href="/assets/post-image/2016-02-29/mocha-graph.png" data-lightbox="mocha-graph" data-title="github graph for mocha">
  <img src="/assets/post-image/2016-02-29/mocha-graph.png" title="github graph for mocha">
</a>

##### Jasmine Github Graph
<a href="/assets/post-image/2016-02-29/jasmine-graph.png" data-lightbox="jasmine-graph" data-title="github graph for jasmine">
  <img src="/assets/post-image/2016-02-29/jasmine-graph.png" title="github graph for jasmine">
</a>
<br><br>


## Jasmine
2.x대로 넘어오면서 Mocha랑 비교되던 `asynchronous testing`이 Mocha와 같이 done을 써서 처리하도록 되었고
이에 따라 크게 Mocha와 비교해서 기능적으로 부족함은 없어졌습니다. 그리고 주목해야할 점은 AngularJS 1.0과 2.0 Beta 모두 Document 개발문서에서 unit-test 부분을
Jasmine을 이용하여 예제를 제공하고 있습니다.
밑에 표와 같이 [2015 Frontend Tooling Servey][Frontend Servey]에서 테스트 프레임워크 투표에서 사용률 1위로 나온 점도 눈여겨 볼 만 합니다.

##### 2015 Frontend Tooling Servey (JavaScript Testing)
<a href="/assets/post-image/2016-02-29/javascript-testing.png" data-lightbox="javascript-testing" data-title="bar graph for 2015 servey javascript testing">
  <img src="/assets/post-image/2016-02-29/javascript-testing.png" title="bar graph for 2015 servey javascript testing">
</a>
<br><br>



## Mocha vs Jasmine
위에 말한대로, 크게 보면 현재 Mocha와 Jasmine의 기능적인 차이점은 없습니다. 둘다 테스팅하기에 부족함이 없고 꾸준히 개발되고 있으며 사용률도 비슷합니다.
다른 점을 보면 일단 `Jasmine`은 Assertion 라이브러리와 Mocking 라이브러리가 `내장`되어 있어 바로 사용할 수 있으며,
`Mocha`는 Assertion과 Mocking에 대해서 `외부 라이브러리를 추가`해서 사용해야 된다는 점입니다.
Jasmine는 사용자가 선택에 부담없이 바로 사용할 수 있고, Mocha는 어떤 외부 라이브러리를 써야될지 선택을 해야합니다.
어쩌면 Mocha의 단점이라고 생각들 수도 있지만 자신에게 맞는 외부 라이브러리를 찾아서 쓰면 되므로 장점이 되기도 합니다.\\
(하지만 Mocha를 사용하는 유저들은 보통 Assertion 라이브러리로 [chai][chai]를, Mocking 라이브러리로 [sinon][sinon]을 많이 사용합니다)

> sinon에는 fake server를 만들어 api를 테스트할 수 있는 기능이 있는데 Jasmine에서는 지원하지 않습니다. 하지만, Jasmine도 언제든지 sinon을 추가해 사용할 수 있습니다. 다만 이미 Mocking 라이브러리가 있는데 또 추가하기가 꺼려집니다.

둘 프레임워크 모두 기능적으로는 부족함이 없고 비슷하기까지합니다. 그래서 더욱 선뜻 고르기가 참 힘듭니다.
회사에서는 Mocha을 사용하는데, 아무래도 좀 더 개발이 활발한 Mocha쪽으로 마음이 기울기도 하고 Airbnb와 TJ 할로웨이 척에 대한 신뢰감이 작용하기도 했습니다.
다른 기타 블로그 글이나 문서를 보더라도 이 선택에 대한 결정은 어쩌면 기능 외 적으로 좀 더 선호하고 신뢰가 가는 쪽으로 선택하면 될 것 같습니다.
<br><br><br>


## 기타 테스트 프레임워크 간단히 보기

## Jest
* Facebook에서 만든 테스트 프레임워크이며, 현재 Facebook에서 사용 중.
* 역시 React에서도 Jest를 사용하여 unit-test의 예로 들고 있다.
* 사용자에 요청에 대한 지원이 원활하지 않아 커뮤니티에서 평판이 좋지 않은 편
* [https://facebook.github.io/jest/][jest homepage]

## Guiter
* 네이버에서 만든 테스트 프레임워크로 참신하고 재미있다.
* 이미지 기반으로 테스팅이 가능하다.
* 한글로 테스팅 스크립트를 작성한다.
* 참신하지만 실제 프로젝트에 적용하긴 힘들 것 같다.
* [http://d2.naver.com/helloworld/1296][D2 Guitar 소개 글]




[Express]: http://expressjs.com
[Jade]: http://jade-lang.com
[Frontend Servey]: http://programmingsummaries.tistory.com/383
[chai]: http://chaijs.com
[sinon]: http://sinonjs.org
[D2 Guitar 소개 글]: http://d2.naver.com/helloworld/1296
[jest homepage]: https://facebook.github.io/jest/