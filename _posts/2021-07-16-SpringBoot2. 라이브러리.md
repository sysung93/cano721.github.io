---

title: "2.SpringBoot 라이브러리"
excerpt: ""

categories: [devlog]

tag: javaSpring

toc: true

toc_sticky: true

date: 2021-07-16

last_modified_at: 2021-07-16
---



# SpringBoot 라이브러리

> **<사전 준비물>**
>
> JAVA 11 설치(사전에 다른 버전이 설치되어있다면 11로 변경 -> 변경 실패 시 자바 다 삭제했다가 새로 까는게 편함..)
>
> IntelliJ 설치
>
> Gradle 프로젝트 생성 : Gradle은 의존관계가 있는 라이브러리를 함께 다운받음



#### 스프링부트 라이브러리

* spring-boot-starter-web
  * spring-boot-starter-tomcat:톰캣(웹서버)
  * spring-webmvc: 스프링 웹 MVC
* spring-boot-starter-thymeleaf:타임리프 템플릿 엔진(View)
* spring-boot-starter(공통): 스트링부트 + 스프링 코어 + 로깅
  * spring-boot
    * spring-core
  * spring-boot-starter-logging
    * logback,slf4j



#### 테스트 라이브러리

* spring-boot-starter-test
  * junit: 테스트 프레임워크
  * mockito[^1]:목 라이브러리
  * assertj: 테스트 코드를 좀 더 편하게 작성하게 도와주는 라이브러리
  * spring-test:스프링 통합 테스트 지원



---

[^1]: 개발자가 동작을 직접 제어할 수 있는 가짜(Mock) 객체를 지원하는 테스트 프레임워크





### 참조

https://mangkyu.tistory.com/145

https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%9E%85%EB%AC%B8-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8

