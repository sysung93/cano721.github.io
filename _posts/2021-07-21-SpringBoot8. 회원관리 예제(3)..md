---

title: "8.SpringBoot 회원관리예제(3)"
excerpt: "회원 웹 기능 - 홈 화면 추가, 등록, 조회"

categories: [devlog]

tag: javaSpring

toc: true

toc_sticky: true

date: 2021-07-21

last_modified_at: 2021-07-21
---





# 회원관리 예제



#### 홈 화면 추가



```
package hello.hellospring.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class HomeController {

    @GetMapping("/")
    public String home(){
        return "home";
    }
}
```

* 컨트롤러 생성
  * 기본 url로 들어왔을 시 home() 실행
  * return "home"을 통해 home.html 랜더링하여 반환



```
<!DOCTYPE html>
<html xmln:th = "http://wwww.thymeleaf.org">
<body>

<div class="container">
  <div>
    <h1>Hello Spring</h1>
    <p>회원 기능</p>
    <p>
      <a href="/members/new">회원 가입</a>
      <a href="/members">회원 목록</a>
    </p>
  </div>
</div>


</body>
</html>
```

* home.html 탬플릿 생성
  * 회원가입 클릭 시 localhost:8080/members/new 로 이동 a 태그 설정
  * 회원목록 클릭 시 localhost:8080/members 로 이동 a 태그 설정



#### 등록

<img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/users/users9.JPG?raw=true">



```
@GetMapping("/members/new")
public String createForm(){
    return "members/createMemberForm";
}
```



* MemberController에 Mapping 설정



```
<!DOCTYPE HTML>
<html xmins:th="http://www.thymeleaf.org">
<body>

<div class="container">

  <form action="/members/new" method="post">
    <div class="form-group">
      <label for="name">이름</label>
      <input type="text" id="name" name="name" placeholder="이름을 입력하세요">
    </div>
    <button type="submit">등록</button>
  </form>
</div>

</body>
</html>
```

* templates에 members 폴더를 만들고 그안에 createMemberForm.html 생성
* 해당 html 코드 작성
  * form 태그를 통해 데이터를 전송
  * input 태그에 type,id,name 등을 설정
  * `button type="submit"` 버튼 클릭 시 `action="/members/new"` 으로`method="post"` post방식으로 전송 



```
package hello.hellospring.controller;

public class MemberForm {
    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

* controller 패키지 아래에 MemberForm 클래스 생성



```
@PostMapping("/members/new")
public String create(MemberForm form) {
    Member member = new Member();
    member.setName(form.getName());

    memberService.join(member);

    return "redirect:/";
}
```

* MemberController에 PostMapping 설정





* 메인페이지에서 회원가입 클릭 시 `@GetMapping("/members/new")`을 통해 `createMemberForm`html 이동
  * <img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/users/users10.JPG?raw=true">
* 해당 url에서 from태그에 데이터를 넣어 버튼을 누르면 `action="/members/new"` 으로 이동
* MemberController에 PostMapping에서 받아서 메소드 호출
  * PostMapping은 데이터를 받을 때 쓰임
* 메소드 내에서 회원 저장
  *  memberService.join(member);



순서

MemberController에 Mapping -> createMemberForm.html -> 웹사이트에서 form 태그에 이름 입력 후 전송

-> MemberController에 PostMapping -> 해당 메소드 실행으로 회원 저장 -> redirect로 메인페이지로 이동



#### 조회



```
@GetMapping("/members")
public String list(Model model){
    List<Member> members = memberService.findMembers();
    model.addAttribute("members",members);
    return "members/memberList";
}
```

* MemberController에 Mapping 설정
  * 기존에 설정해두었던 전체조회 메소드 실행
    * memberService.findMembers()
  * model에 해당 데이터 담기
    * model.addAttribute[^1] ("members",members)
    * html에서 members로 부르면 list형태로 담긴 데이터가 반환됨
  * memberList.html로 리턴



```
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<body>

<div class="container">
  <div>
    <table>
      <thread>
        <tr>
          <th>#</th>
          <th>이름</th>
        </tr>
      </thread>
      <tbody>
      <tr th:each="member : ${members}">
        <td th:text="${member.id}"></td>
        <td th:text="${member.name}"></td>
      </tr>
      </tbody>
    </table>
  </div>
</div>

</body>
</html>
```

* memberList.html 생성
* th:each : 루프 도는 명령어(thymeleaf명령어)
  * member.id를 출력
  * member.name을 출력



**결과**

<img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/users/users10.JPG?raw=true">



데이터가 안나올경우

* 회원가입을 통해 데이터를 넣었는지 확인해볼것.



---

[^1]:Model addAttribute(String name, Object value) - value 객체를 name 이름으로 추가한다. 뷰 코드에서는 name으로 지정한 이름을 통해서 value를 사용한다.

### 참조

[김영한님의 인프런강의](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%9E%85%EB%AC%B8-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8)
