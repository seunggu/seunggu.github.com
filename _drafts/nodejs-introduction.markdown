---
layout: post
title: "Node.js에 대한 고찰과 도입 "
date: 2016-07-31 00:00:00
author: 승구
categories: [node.js]
tags: [node.js, tech stack]
cover: "/assets/black-background.png"
---

## 1. 최근 유명 기업들은 왜 Node.js 도입하는가

최근 많은 해외 유명 기업들이 `Node.js`를 사용하고 있다는 걸 들어보신 적이 있으실겁니다.
그리고 점차 그 추세가 증가하고 있습니다.
대표적인 기업으로는 최근에 MS에 인수가 된 **LinkedIn**, 올해부터 국내에도 서비스를 시작한 **Netflix**, 
해외 결제 하실 때마다 보게되는 **PayPal**이 있습니다.
위 기업들은 세계적으로 유명하고 엄청난 트래픽을 처리하죠. 
2014년 이후부터 기업에서도 Production 환경에서 `Node.js`를 사용하는 일이 실제 일어나고 있습니다.
이 글에서 기업들이 말한 Node.js를 도입하게 된 이유와 느낀 점을 정리하면서 `Node.js`에 대해 조금 더 현실적이고 깊게 이야기를 해보려고 합니다.
(우선 기업들이 도입을 결정한 이유에는 장점만 언급한 경우가 많고, 후에 단점들을 함께 얘기하며 도입시 고려사항을 정리하겠습니다)
<br><br>

## 2. LinkedIn, 높은 효율성

> \-  **LinkedIn**<br>
> One of LinkedIn’s biggest win was the 10:1 ratio reduction in the number of machines used to host their services after switching from Ruby on Rails to Node.js


`LinkedIn`은 기존 RoR서버를 Node로 변경하면서 인프라를 1/10로 줄이게 됬다고 합니다.
이에 대한 장점은 GoDaddy도 언급한 장점이기도 합니다. `GoDaddy`는 기존 C#으로 작성한 서버의 인프라의 단 **1/10**만으로
여전히 같은 (오히려 더 많은!!) 트래픽을 처리하고 있다고 합니다. 이 글이 작성된 시기의 `GoDaddy` 트래픽은 **초당 170만**이였다고 합니다.
이렇게 높은 효율의 트래픽 처리가 가능한 것은 Node.js의 **Single Thread 기반의 비동기 처리 IO** 때문이죠.
보통 동기 프로그래밍에선 IO 작업을 할 경우 처리를 기다리는데 비용을 낭비하게 되는데 
Node.js는 IO작업을 요청하고 다른 일을 하고 요청한 IO작업이 처리가 된 후에 이벤트를 받아 그 후의 일을 계속하기 때문에 효율적으로 IO처리를 하게 되죠.
그래서 보통 Node의 장점에 [c10k problem](http://www.kegel.com/c10k.html)에 대해 최적화 되어 있음을 많이 얘기합니다.
<br><br>


## 3. PayPal & Netfilx, 높은 생산성

> \- **PayPal**<br>
> Node.js and an all Javascript development stack helped PayPal bring efficiencies in engineering and helped rethink and reboot product, design and operational thinking." - Sameera Rao, Sr. Business Products Engineering Manager<br>
> \- **Netflix**<br>
> Using Node.js at Netflix gave a productivity boost to the engineers and improved user experience for the customers.

`PayPal`의 경우엔 기존 프론트엔드와 백엔드의 언어가 달랐는데 백엔드를 Node.js로 변경하면서
프론트엔드와 백엔드의 언어가 통일되고 팀 전체의 효율이 증가한 것에 큰 도움을 받았다고 합니다.
사실 Node.js가 유명세를 크게 타게 된 것도 프론트엔드 개발자들이 백엔드에 쉽게 다가갈 수 있다는 점이 크게 작용한 것도 사실이고,
Node로 개발을 하는 FullStack 개발자가 많은 것도 사실입니다.
그 만큼 Node가 Javascript란 언어로 다가갈 수 있다는 점이 크게 작용합니다.
보통 Java와 생산성을 비교할 때도 Node를 이용하면 **1/10**의 시간밖에 들지 않는다고 이야기를 합니다.
많이들 알고 계시지만 **npm(node package manager)**의 영향력도 무시할 수 없습니다.
많은 모듈들이 사용가능함에 따라 Node 생태계에 들어오는 장벽이 많이 부서졌다고 생각합니다.
<br><br>

## 4. 기타 도입시의 장점

위에서 크게 두가지의 장점을 언급했는데요, 앞처럼 큰 장점은 아니지만 간단한 `(Web)Socket`를 구현할 때도 좋습니다.
Node.js를 한번 경험해보셨으면 [Socket.io](http://socket.io)라고 많이 아실거라 생각합니다.
그리고 무엇보다 빠르게 ``프로토타이핑``을 해야하는 스타트업 같은 경우 높은 생산성 때문에 좋습니다.
이 경우엔 동적언어를 사용하는 프레임워크(RoR, Django 등) 모두 해당되는 장점입니다.
하지만 MongoDB와 함께 간단한 프로젝트를 만든다면 Node.js와 호환성이 좋으니 Node.js가 월등히 좋죠.
현재 Node.js 프로젝트를 지원하는 기업들(IBM, Intel, MS 등)이 늘어나고 있다는 점도 좋게 볼 수 있습니다.
Javascript가 [CPS 패턴](https://en.wikipedia.org/wiki/Continuation-passing_style)으로 작성되다 보니
코드의 가독성이 안좋다란 말을 많이 듣게 되었는데요. Node.js의 버전 v6에서는 
[ES6](http://es6-features.org/#Constants)를 지원하므로 예전의 문제였던 가독성도 더 좋아졌습니다.
<br><br>

## 5. 도입시 꼭 고려해야할 단점

위에 이야기들은 모두 Node의 장점들을 나열 하였는데요.
이제 보여드릴 단점 때문에, Node.js의 도입이 이루어지지 않는 경우가 많습니다.
우선 Java의 프레임워크와 비교하면 역사가 짧고 `Enterprise 환경`에서 **검증되지 않은 점**입니다.
위의 기업들이 Production 환경에서 사용하고 있는 점들은 굉장히 힘이 되는 부분이지만, 
아직은 **의심의 눈초리**로 Node.js를 보는 입장이 많습니다.
이미 Java 프레임워크들은 현재까지 트래픽이 많은 Enterprise 환경에서 잘 동작해주었고,
그 만큼 검증이 되었으며 안정적이고 사내 개발자 풀도 많습니다. 
국내에선 전자정부 프레임워크라는 장벽도 무시할 수 없구요.
<br><br>
그리고 Node가 Single Thread에서 돌아가는지라 하나의 cpu 작업에 시간이 많이 걸리면, 전체적인 시스템 성능이 굉장히 떨어집니다.
그래서 cpu가 많이 사용되는 프로젝트에선 좋지 않습니다.
실제로 cpu를 많이 사용하게 된 프로젝트를 Node로 한 적이 있었는데 cpu가 정말 기하급수적으로 올라갑니다.
그리고 국내에서 도입할 경우엔 서비스내에서 이메일이나 문자 또는 본인인증관련해서 사용하게 될 경우가 있을 수 있는데,
국내 서드파티에선 Node.js를 잘 지원하지 않는 점도 큰 단점 중 하나입니다.
이메일이나 문자 정도는 해외 서비스를 사용한다고 하지만, 본인인증관련해서는 국내 서비스를 사용할 수 밖에 없으니 생각보다 큰 걸림돌입니다.
<br><br>

## 6. 개인적인 도입가이드

저의 개인적인 생각으론 Node.js를 **우호적**으로 바라보고 있습니다. **높은 효율성과 생산성** 때문인데요.
하지만, `Node.js`를 도입해서 효율을 끌어낼 수 있는 상황과 오히려 반대인 상황을 잘 판단하셔야 합니다.
Node.js는 **장단점이 확실**하고 잘 사용해야 큰 효율을 발휘 할 수 있습니다.
만약 프로젝트가 무겁고 cpu를 많이 사용한다면 Node.js의 도입은 당연히 좋지 않습니다.
여러 문제가 터질 수 있고 동적언어를 이용하다보니 디버깅도 쉽진 않습니다.
하지만 단순 DB작업을 하거나 간단한 프로젝트일 경우가 Node.js에 적합합니다.
웹서버로써의 역활도 좋고, 생산성도 뛰어나고 효율도 좋은 장점을 적극 활용할 수 있는 프로젝트에서 사용하면 좋습니다.
예를들면, 스타트업에서 프로토타입을 만들기에도 적합하고 인원이 적은 개발팀에서 웹서비스를 만들때에 적은 인원으로 효율을 낼 수 있습니다.
확실히 이런 장단점이 존재하니 잘 판단하셔서 도입을 하시면 좋은 결과를 낼 수 있을 것 같습니다.
<br><br>


## 7. 장점 정리

* 인프라를 적게 사용하는 높은 효율성 == 비용 절감
* 많은 요청을 견대낼 수 있는 힘(c10k 최적화)
* 높은 생산성
* Front-end와 Back-end 모두 Javascript로 가능
* 커져가는 생태계 (npm, 참여하는 기업들 등)
* 프로토타이핑에 좋음
* v6부터 ES6로 가독성이 더 좋아짐
<br><br>


## 8. 단점 정리

* cpu 작업에 약함
* 국내에서 사용하기엔 제약이 존재 (국내 써드파티의 지원, 개발자 풀 등)
* 역사가 짧아 안정적이지 않음
* Enterprise 환경에서의 역활에 대한 의심의 눈초리
<br><br>


## 9. 참고자료

* [Node.js is Enterprise Ready](https://risingstack.com/nodejs-is-enterprise-ready?utm_source=how-enterprises-benefit-from-nodejs&utm_medium=rsblog&utm_campaign=whitepaper)
* [How Enterprises Benefit form Node.js](http://docs.aws.amazon.com/AmazonS3/latest/dev/Versioning.htmlhttps://blog.risingstack.com/how-enterprises-benefit-from-nodejs/)
* [Node.js에 대한 단상](http://rocksea.tistory.com/253)
* [Node.js에 장단점에 대해](http://bcho.tistory.com/876)



