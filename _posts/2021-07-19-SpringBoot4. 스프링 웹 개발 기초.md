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





#### API

템플릿 엔진을 통한것과 다르게 데이터 그대로 반환해줌(소스코드 보기하면 데이터만 있음)

객체로 반환할 시 json으로 반환하는게 default로 설정되어있음



```
@GetMapping("hello-string")
@ResponseBody
public String helloString(@RequestParam("name") String name){
    return "hello " + name; // "hello spring"
}

# 웹사이트 localhost:8080/hello-spring?name=spring 검색 시 데이터 반환


@GetMapping("hello-api")
@ResponseBody
public Hello helloApi(@RequestParam("name") String name){
    Hello hello = new Hello();
    hello.setName(name);
    return hello;
}

static class Hello {
    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}

# 웹사이트 localhost:8080/hello-api?name=spring 검색 시 json형태 데이터 반환
```



아래와 같은 객체 반환 형태의 api

게터와 세터를 통해 데이터를 세팅하거나 확인 가능(프로퍼티 방식)



<img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/javaSpringBasic/javaSpringBasic4.JPG?raw=true">



* 톰캣서버에서 스프링으로 전달
* 매핑되어있는 컨트롤러 호출
* @ResponseBody로 붙어 있는 경우
  * httpMessageConverter가 동작하며 HTTP에 BODY에 문자 내용을 직접 반환
    * 단순 문자: StringConverter가 동작하여 처리
    * 객체: JsonConverter가 동작하여 json형태로 처리
    * byte 처리 등등 기타 여러 httpMessageConverter가 기본으로 등록되어 있음



**참고**

클라이언트의 HTTP Accept 헤더와 서버의 컨트롤러 반환 타입 정보 둘을 조합해서

'HttpMessageConverter'가 선택된다. (추후 강의 예정)



#### MVC탬플릿엔진 VS API

* 'viewResolver' : 'httpMessageConverter'
* 탬플릿을 랜더링한 후 html형태로 반환 : 데이터 직접 반환





### 참조

[김영한님의 인프런강의](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%9E%85%EB%AC%B8-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8)
