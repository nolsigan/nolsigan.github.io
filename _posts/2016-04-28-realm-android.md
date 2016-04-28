---
layout: post
date: 2016-04-28
title: Android에서 Realm을 통해 db 관리하기
tags: [android, realm, db]
---

스스로 공부하기 위해서 안드로이드 앱을 만들고 있다. 제대로 된 안드로이드 앱을 만들어 본게 외주 일로 prototype이 나와 있는 
앱을 완성 시켜본게 다여서 db 관리를 어떻게 해야할지부터 고민해야 했다.
외주 때 앱은 sql 구문을 직접 써서 관리를 하고 있었는데 좀 더 abstract한 방법이 있을 것 같아 찾아보다가 Active Android를 접하게 되었다.

<br/>

# Active Android

[Github][AA_github] \| [Guide][AA_guide]

Active Android는 ORM ( Object-Relational Mapping ) 을 통해 sql query를 direct하게 사용하지 않고 간단한 API를 통해 db 관리를 지원한다.  
사용법도 간단하고 guide, example도 많아서 이거다! 싶었지만 알고 보니 개발이 중지된지 꽤 된 프로젝트였다. 그래서 db schema를 다 짜 놨는데 고민하다 결국 
더 트렌디한 걸 써보기로 결정하고 Realm으로 갈아탔다.

<br/>

# Realm-java

[Github][Realm_github] \| [Homepage][Realm_page]

Realm은 안드로이드 이외에도 ios, js 도 지원한다. 특이한게 외국 회사임에도 불구하고 documentation의 한글 지원이 굉장히 잘 되어있는데 팀에 한국인 개발자가 있다고 한다.  
보니까 Y Combinator에서 인큐베이팅 받는 것 같던데 부럽다..

여튼 본론으로 들어가서 Realm은 sql도 아니고 ORM도 아니며 자체 엔진을 통해 simple하고 빠른 db 관리를 제공한다.

Schema Object도 다음과 같이 뚝딱 정의할 수 있고

```java
public class Person extends RealmObject {
    @Required // Name is not nullable
    private String name;
    private String imageUrl; // imageUrl is an optional field
    private RealmList<Dog> dogs; // A person has many dogs (a relationship)

    // ... Generated getters and setters ...
}
``` 

정말 좋다고 생각하는건 오픈소스 프로젝트라 기능이 굉장히 다양하다. realm document에서 다른 개발자가 realm을 이용하여 만든 package를 소개하기도 한다.
사용자 입장에선 정말 좋은데 돈을 어디서 벌지..? 궁금궁금~

한 가지 재밌었던건 지금 학교에서 OS 수업을 듣고 있는데 Synchronization에 대해 배웠던게 realm에서도 쓰였다 ㅋㅋ  
Linux에서 Sync를 위해 lock을 걸듯이 transaction 시 개발자가 직접 lock을 걸어줘야한다.

```java
realm.beginTransaction();

Person nPerson = new Person();
nPerson.set(...)

realm.commitTransaction();
```

굉장히 low level 에서만 쓰이는 줄 알았는데 안드로이드에서 만나니 뭔가.. 반가웠다.. ㅎ


## realm-recyclerview

[Github][rr_github] \| [Example][rr_example]

db를 활용할 때 listview를 다룰 일이 많은 데 이 소스가 완벽하게 해결해줬다. 아직 개발 중인 프로젝트라 완전 안정적이진 않은 것 같은데 제공하는 기능들이 
매력적이다.

* realm object 연동하여 바뀌면 auto-update
* 밀어서 삭제 기능 제공!!

example도 잘 설명되어 있어서 별 어려움 없이 이용할 수 있었다.

realm에서 react도 지원하던데 react를 빨리 공부해봐야겠다. 배울 게 너무나 많다 :)  
화이팅


[AA_github]: https://github.com/pardom/ActiveAndroid
[AA_guide]: https://github.com/codepath/android_guides/wiki/ActiveAndroid-Guide
[Realm_github]: https://github.com/realm/realm-java
[Realm_page]: https://realm.io/
[rr_github]: https://github.com/thorbenprimke/realm-recyclerview
[rr_example]: https://realm.io/news/android-realm-listview/