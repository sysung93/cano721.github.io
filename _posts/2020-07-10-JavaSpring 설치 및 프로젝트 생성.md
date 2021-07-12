---

title: "JavaSpring 설치 및 프로젝트 생성"
excerpt: "shallow copy와 deep copy 차이점 알아보기"

categories: [devlog]

tag: javaSpring

toc: true

toc_sticky: true

date: 2021-07-10

last_modified_at: 2021-07-10
---



# JavaSpring 설치

> <사전 준비물>
>
> JAVA 11 설치(사전에 다른 버전이 설치되어있다면 11로 변경)
>
> IntelliJ 설치





https://start.spring.io/ 접속

: 스프링 관련 프로젝트 생성 사이트



![1`](md-images/자바스프링설치1.jpg)



#### Project

* Maven Project
  * 필요로하는 라이브러리와 전체적인 라이프사이클까지 관리해주는 툴
  * XML[^1]로 라이브러리를 정의하고 활용

* Gradle Project
  * 빌드 배포 도구 - 별도의 빌드 스크립트를 통해 사용할 어플리케이션 버전, 라이브러리등 항목 설정 가능



예전엔 Maven이었지만 최근은 다 Gradle로 설정함



#### Gradle을 사용하는 이유

* Build는 동적인 요소인데 XML로 정의하긴 어려운 부분이 있음
  * 설정 내용 길어지고 가독성 하락
  * 의존관계 복잡 프로젝트 설정으로 부적절
  * 상속구조를 이용한 멀티 모듈 구현
  * 특정 설정을 소수의 모듈에서 공유하기 위해선 부모 프로젝트를 생성해서 상속해야함(상속의 단점)
* Gradle은 그루비(Groovy)[^2] 사용, 동적 Build는 Groovy script로 플러그인을 호출 또는 직접 코드 작성
  * Configuration Injection[^3] 방식을 사용 -> 공통 모듈 상속해서 사용하는 단점[^4]을 커버
  * 설정 주입 시 프로젝트 조건을 체크할 수 있어서 프로젝트별로 다르게 설정 가능



#### 결론

> Gradle이 Maven에 비해 최대 100배 빠르다
>
> Maven의 단점 해결 가능!





---

 [^1] XML(eXtensible Markup Language) : HTML과 매우 비슷한 문자 기반의 마크업 언어(추후 정리)

 [^2] JVM에서 실행되는 스크립트 언어

 [^3] 설정 주입 방식으로, maven에 비해 보다 유연하게 설정 주입이 가능하단 뜻으로 해석

 [^4] 상속구조이기에 부모 모듈의 기능 변경이 하위모듈에도 영향이 갈 수 있음.

