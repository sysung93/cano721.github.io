---
title: "6. BeanFactory 와 ApplicationContext 차이점"
excerpt: ""

categories: [devlog]

tag: javaSpring

toc: true

toc_sticky: true

date: 2021-11-09

last_modified_at: 2021-11-09

---



# BeanFactory와 ApplicationContext 차이점



<img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/springCore/springCore21.JPG?raw=true">



**BeanFactory**

* 스프링 컨테이너의 최상위 인터페이스다.
* 스프링 빈을 관리하고 조회하는 역할을 담당한다.
* `getBean()` 을 제공



**ApplicationContext**

* BeanFactory 기능을 모두 상속받아서 제공한다.
* 빈을 관리하고 검색하는 기능을 BeanFactory가 제공해주는데, ApplicationContext는 그 외의 부가기능도 제공해준다.(다른 것들도 상속받음!)
* <img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/springCore/springCore22.JPG?raw=true">
  * **메시지소스를 활용한 국제화 기능**
    * 예를 들어서 한국에서 들어오면 한국어로, 영어권에서 들어오면 영어로 출력
  * **환경변수**
    * 로컬,개발,운영등을 구분해서 처리
  * **애플리케이션 이벤트**
    * 이벤트를 발행하고 구독하는 모델을 편리하게 지원
  * **편리한 리소스 조회**
    * 파일,클래스패스,외부 등에서 리소스를 편리하게 조회



**정리**

* ApplicationContext는 BeanFactory의 기능을 상속받음.
* ApplicationContext는 빈 관리기능 + 편리한 부가 기능을 제공한다.
* BeanFactory를 직접 사용할 일은 거의 없음. 부가기능이 포함된 ApplicationContext를 사용한다.
* BeanFactory나 ApplicationContext를 스프링 컨테이너라 한다.





### 참조

[김영한님의 인프런강의](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8)

