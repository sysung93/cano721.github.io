---

title: "3.SpringBoot View 환경설정 & Build"
excerpt: "View 환경설정,SpringBoot 실행"

categories: [devlog]

tag: javaSpring

toc: true

toc_sticky: true

date: 2021-07-16

last_modified_at: 2021-07-18
---



# SpringBoot View 환경설정 & Build

> **<사전 준비물>**
>
> JAVA 11 설치(사전에 다른 버전이 설치되어있다면 11로 변경 -> 변경 실패 시 자바 다 삭제했다가 새로 까는게 편함..)
>
> IntelliJ 설치
>
> Gradle 프로젝트 생성 : Gradle은 의존관계가 있는 라이브러리를 함께 다운받음





#### Welcome Page 만들기



<img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/viewSetting/viewSetting1.JPG?raw=true">

 

* src - main - resources - static
  * static에서 마우스 오른쪽 버튼 -> new ->HTML file 생성
* 아래 이미지와 같이 코드 설정
* [localhost:8080](localhost:8080) 가면 hello 뜸



[spring사이트에서 확인하기](https://docs.spring.io/spring-boot/docs/current/reference/html/features.html#features.developing-web-applications)

해당 사이트에서 7.1.6에서 확인가능



#### Thymeleaf 탬플릿 엔진

[공식사이트](https://www.thymeleaf.org/)



#### Controller 생성 및 설정

<img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/viewSetting/viewSetting2.JPG?raw=true">

* hello.hellospring 하위에 controller 패키지 생성
  * controller 패키지 아래에 controller 클래스 생성
  * 그림과 같이 코드 작성



#### 탬플릿 설정

<img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/viewSetting/viewSetting4.JPG?raw=true">

* templates 폴더 하위에 html 파일 생성
* 그림과 같이 코드 작성
* th가 Thymeleaf 탬플릿 엔진 사용



img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/viewSetting/viewSetting3.JPG?raw=true">



```
@Controller
public class HelloController {

    @GetMapping("hello")
    public String hello(Model model){
        model.addAttribute("data","hello!!"); # key,value
        return "hello";
    }
}
```

웹사이트에서 localhost:8080/hello 로 접속 시 톰캣에서 spring으로 전달

그럼 get방식으로 controller 코드에 있던 @GetMapping에 적힌 "hello" 에 매칭

해당 메소드 모델을 실행하게 되고 키: "data" 벨류: "hello!!" 를 확인하고

return hello 가 templates폴더 내 hello.html을 랜더링하게 됨



(컨트롤러 리턴 값 문자 반환 -> viewResolver가 해당 html을 찾아서 처리)

viewResolver 찾는방법: 'resources:templates/' + 리턴값 + '.html'



그래서 hello.html에 있던 

```
<p th:text="'안녕하세요. '+ ${data}">안녕하세요. 손님</p>
```

data 에 hello! 가 반환됨





#### Build



cmd 콘솔창에서 입력

````
<윈도우 기준!>

해당 파일 경로로 이동

cd ~~ : 경로 이동 명령어
cd .. : 이전 경로 이동 명령어
dir : 해당 경로에 있는 파일과 하위 디렉토리 목록 보여줌

C:\IOT\work\javaSpring\hello-spring>

해당경로로 이동한 다음 순차적으로 명령어 입력

gradlew bat

gradlew build

빌드 파일 설치되었을거임
다시 경로 이동

cd build
cd libs

C:\IOT\work\javaSpring\hello-spring\build\libs

dir 명령어 입력하면

18,825,924 hello-spring-0.0.1-SNAPSHOT.jar

해당 파일 존재

java -jar hello-spring-0.0.1-SNAPSHOT.jar 명령어 입력

스프링 실행완료

````



추후에 배포 시엔 

hello-spring-0.0.1-SNAPSHOT.jar

이 파일만 복사해서 서버에서 실행하면 됨



**요약!**

1. 콘솔(cmd)로 이동
2. 파일 경로 이동
3. gradlew bat 명령어 입력
4. gradlew build 명령어 입력
5. \build\libs 경로로 이동
6. java -jar hello-spring-0.0.1-SNAPSHOT.jar 명령어 입력
   1.  hello-spring의 경우 파일명!
7. 웹사이트에서 실행 확인







### 참조

[김영한님의 인프런강의](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%9E%85%EB%AC%B8-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8)
