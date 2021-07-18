---

title: "4.SpringBoot 스프링 웹 개발 기초"
excerpt: "정적 컨텐츠 구조, MVC와 탬플릿 엔진, API"

categories: [devlog]

tag: javaSpring

toc: true

toc_sticky: true

date: 2021-07-19

last_modified_at: 2021-07-19
---





# 스프링 웹 개발 기초



#### 정적 컨텐츠 구조



<img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/javaSpringBasic/javaSpringBasic2.JPG?raw=true">

 

* 해당 html 파일 생성 및 코드 삽입
* 프로젝트 실행

* [localhost:8080/해당파일명](localhost:8080/hello-static.html) 가면 해당 내용 뜸

  



<img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/javaSpringBasic/javaSpringBasic1.JPG?raw=true">



* 웹 브라우저에서 요청이 들어오면 내장 톰캣서버가 스프링으로 전달
* hello-static에 관련된 컨트롤러 유무 확인
* 없으면 resources:static/hello-static.html을 찾음
* 찾은 것 반환





#### MVC와 템플릿 엔진



MVC: Model, View, Controller

장고는 MTV : Model, Templete, View



Model엔 화면에 필요한것들을 담아줘서 넘겨줌

Controller는 내부로직을 구성하는것

View는 화면을 그리는데 역량이 집중되어있음



Controller 설정

```
@GetMapping("hello-mvc")
    public String helloMvc(@RequestParam(value = "name") String name,Model model){
        model.addAttribute("name",name);
        return "hello-template";
    }
```

* @RequestParam(value = "name") : 웹브라우저상의 키값
  * ex) localhost:8080/hello-mvc?**name**=spring
* model.addAttribute("name",name) : 템플릿에 넘길 키와 벨류값
  * ex) <p th:text="'hello ' + ${name} "></p>
  * ${name} 에 벨류값(spring)으로 반환





template 설정

```
<html xmlns:th = "http://www.thymeleaf.org">
<body>
<p th:text="'hello ' + ${name} ">hello! empty</p>
</body>
</html>
```



* 해당 컨트롤러와 템플릿을 설정 후
* [localhost:8080/hello-mvc?name=spring](localhost:8080/hello-mvc?name=srpingl) 접속하면
* hello spring이란 글이 뜸



<img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/javaSpringBasic/javaSpringBasic3.JPG?raw=true">



* 톰캣서버를 통해 스프링으로 내용전달
* 매핑되어있는 컨트롤러를 호출
* 설정되어있는 탬플릿에 내용과 함께 리턴
* 뷰리졸버가 탬플릿을 연결해줌
* Thymeleaf 탬플릿 엔진에서 랜더링하여 변환하여 처리





### 참조

[김영한님의 인프런강의](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%9E%85%EB%AC%B8-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8)
