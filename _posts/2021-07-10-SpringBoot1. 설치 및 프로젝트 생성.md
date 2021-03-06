---

title: "1.JavaSpring 설치 및 프로젝트 생성"
excerpt: "shallow copy와 deep copy 차이점 알아보기"

categories: [devlog]

tag: javaSpring

toc: true

toc_sticky: true

date: 2021-07-10

last_modified_at: 2021-07-10
---



# JavaSpring 설치 & 프로젝트 생성

> **<사전 준비물>**
>
> JAVA 11 설치(사전에 다른 버전이 설치되어있다면 11로 변경 -> 변경 실패 시 자바 다 삭제했다가 새로 까는게 편함..)
>
> IntelliJ 설치





[https://start.spring.io/](https://start.spring.io/) 접속

: 스프링 관련 프로젝트 생성 사이트





<img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/javaSpringSetting/javaSpringSetting1.JPG?raw=true">



#### Project

* Maven Project
  * 필요로하는 라이브러리와 전체적인 라이프사이클까지 관리해주는 툴
  * XML[^1]로 라이브러리를 정의하고 활용

* Gradle Project
  * 빌드 배포 도구 - 별도의 빌드 스크립트를 통해 사용할 어플리케이션 버전, 라이브러리등 항목 설정 가능



예전엔 Maven이었지만 최근은 다 Gradle로 설정함



#### Language

* 사용언어 설정



#### Spring Boot

* 사용할 스프링부트 버전



#### Project Metadata

* Group : 회사명을 일반적으로 적음
* Artifact : 프로젝트명
* 기타 등등



#### Dependencies

* Dependency 의존성이란 뜻으로 사용할 라이브러리 추가
* Spring Web : Web 프로젝트 진행예정이여서 추가
* Thymeleaf : 템플릿 엔진. 뷰 탬플릿이라고도 부르며 컨트롤러가 전달하는 데이터를 이용하여 동적으로 화면을 구성하게 해줌.



이렇게 설정이 다 끝나면

#### GENERATE 를 클릭하여 다운 및 압축 풀기





***

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

[^1]: XML(eXtensible Markup Language) : HTML과 매우 비슷한 문자 기반의 마크업 언어(추후 정리)
[^2]: JVM에서 실행되는 스크립트 언어
[^3]: 설정 주입 방식으로, maven에 비해 보다 유연하게 설정 주입이 가능하단 뜻으로 해석
[^4]: 상속구조이기에 부모 모듈의 기능 변경이 하위모듈에도 영향이 갈 수 있음.





# 프로젝트 실행



IntelliJ에서  Open으로 압축 푼 해당 프로젝트 실행

자동으로 관련 gradle import 해줌

안해주면 IntelliJ 닫았다가 다시 실행



<img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/javaSpringSetting/javaSpringSetting2.JPG?raw=true">



왼쪽 폴더에서

hello-spring -> src -> main -> java -> hello.hellospring -> HelloSpringApplication 클릭

위의 그림의 화살표 클릭해서 실행



<img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/javaSpringSetting/javaSpringSetting3.JPG?raw=true">

브라우저에서 http://localhost:8080/ 검색 시

해당 이미지와 같이 나온다면 정상적인 실행!







### 참조

[김영한님의 인프런강의](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%9E%85%EB%AC%B8-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8)
