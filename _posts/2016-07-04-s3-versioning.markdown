---
layout: post
title: "S3 Versioning에 대해 알아보기"
date: 2016-07-04 05:24:00
author: 승구
categories: aws
tags: aws, s3, s3 versioning
cover: "/assets/black-background.png"
---

## 1. S3 Versioning

최근에 S3을 주로 사용하는 프로젝트를 진행하고 있습니다.
비지니스 로직에 필요한 부분이 같은 파일(같은 S3 key를 가지는 파일)을 조금씩 수정하여 S3에 저장하는
프로세스가 있어 S3에 문서를 훓어 보다가 `S3 Versioning`이라는 것을 발견했습니다.
단순히 설명을 하면, Git처럼 S3에 올린 파일에 대해서 버전관리를 해주는 것인데요.
비지니스 로직이 단순히 S3에 파일을 저장하고 그게 끝이라면 딱히 필요없는 기능이지만,
현재 진행하는 프로젝트처럼 동일한 파일을 다시 자주하는 수정하는 로직이 필요하다면 
생각해볼만한 좋은 기능입니다.
<br><br>


## 2. Version id

S3에 Bucket은 기본적으로 Versioning이 켜지지 않은 상태로 되어있고 
Versioning은 **Bucket 단위**로만 켜고 끌 수 있습니다.
Versioning을 시작하려면 AWS Console에서는 간단히 변경할 수 있습니다.
아래의 사진처럼 Bucket Property에 Versioning 관련 속성이 있어서 
**Enable Versioning** 버튼만 누르면
해당 Bucket은 Versioning이 켜지게 됩니다.

![s3-console](/assets/s3-versioning/console.png)

Bucket의 Object(파일)은 Versioning이 켜져 있든 아니든 
모두 `version id` 메타데이터를 가지고 있습니다.
그리고 Versioning이 꺼져 있을 경우는 **null** 값을 가지게 되고 
Versioning을 키고 Object를 업로드 할 경우에 `version id`에 S3에서 **자동**으로 지정하게 됩니다.
이렇게 Versioning을 `version id`를 기준으로 작동하게 됩니다.
(예를 들면 Git의 **commit hash**와 비슷하다고 볼 수 있습니다.)
<br><br>


## 3. current vs noncurrent

S3 Versioning은 이 version id를 가지고 Object들을 관리하게 됩니다.
기존에 파일을 하나 업로드한 뒤 S3 Key가 동일하도록 같은 이름의 파일을 다시 업로드 하면
Versioning은 기존 파일 위에 파일을 저장하게 됩니다.
그리고 파일에 접근 할 땐 제일 최근의 것으로 접근하게 됩니다.
여기서 제일 최근의 Object를 `current version`라 부르고 
기존의 Object를 `noncurrent version`이라 부릅니다.
S3 Key를 이용하여 Object를 불러오면 `current version`의 Object를 가져오지만
`noncurrent version`의 Object를 가져오려면 **version id**를 사용하여 가져 올 수 있습니다.

![version-id-console](/assets/s3-versioning/version-id-console.png)

<br><br>


## 4. Delete Marker

당연히 Versioning을 이용하면 삭제된 Object도 복구 할 수 있습니다.
이런 기능이 가능한 이유는 Versioning을 켜놓았을 경우 S3는 해당 Object를 삭제 하지 않습니다.
그 대신 Version id `Delete Marker`를 **current version**으로 설정합니다.
그래서 삭제한 경우엔 **current version**이 `Delete Marker`이기 때문에
삭제된 문서로 인식하지만, **noncurrent version**의 Object로 
언제든지 복구가능하고 가져 올 수 있습니다.

![delete-marker](/assets/s3-versioning/delete-marker.png)

![delete-maker-console](/assets/s3-versioning/delete-marker-console.png)

<br><br>


## 5. Lifecycle Configuration for a Bucket with Versioning

기본적으로 S3는 `Lifecyle Configuration`을 지원합니다.
간단한 예를 들면, 만약 프로젝트에서 S3에 Object를 생성하고 
해당 Object가 생성 후 1년뒤에 필요가 없어진다면 
굳이 필요없는 Object를 저장해 둘 필요가 없습니다.
S3는 저장된 용량에 따라 월비용을 내야되기 때문에 
사용하지 않는 Object는 지워주는게 비용을 절감할 수 있습니다.
`Lifecyle Configuration`을 통해 Object를 생성 후 1년뒤에 삭제되도록 설정하면
자동으로 지워지게 되기 때문에 신경을 크게 쓰지않고 비용을 절감 할 수 있습니다

Versioning의 좋은 점은 **current version**과 **noncurrent version**에 대해
각각 따로 `Lifecyle Configuration`이 가능하기 때문에 더욱 쉽게 Object들을 관리하여
비용을 절감 할 수 있습니다. 저도 이 장점이 Versioning을 사용하게된 큰 이유 중 하나입니다.
(Lifecycle에 대한 내용은 이 글에서 주제에 벗어나므로 참고자료에 링크 걸어 두었습니다)
<br><br>


## 6. 장점

* version에 따른 Lifecycle Configuration이 가능하다.
* 같은 파일에 대해 덮어쓸 때 기존의 데이터도 보관하고 싶은데 비용이 걱정될 때가 적절하다.
* Versioning을 사용하지 않을 때와 비용하여 동일한 
Object를 삭제할 때 일일이 찾아서 삭제 하지않고 한번에 삭제 가능하다.
* 삭제한 Object를 복구할 수 있다.
<br><br>


## 7. 단점

* 아무래도 널리 사용되는 S3의 기능이 아니다 보니 다른 개발자가 와서 일을 할 시에 배워야한다.
* Bucket 단위로만 Versioning 설정이 되어 아쉽다.
<br><br>


## 8. 참고자료

* [AWS 공식 문서(Object Lifecycle Management)](http://docs.aws.amazon.com/AmazonS3/latest/dev/ObjectVersioning.html)
* [AWS 공식 문서(Using Versioning)](http://docs.aws.amazon.com/AmazonS3/latest/dev/Versioning.html)
* [AWS 공식 문서(Lifecycle Configuration for a Bucket with Versioning)](http://docs.aws.amazon.com/AmazonS3/latest/UG/lifecycle-configuration-bucket-with-versioning.html)



