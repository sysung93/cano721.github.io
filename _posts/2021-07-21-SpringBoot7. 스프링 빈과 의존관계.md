---

title: "7.SpringBoot 스프링 빈과 의존관계"
excerpt: "컴포넌트 스캔과 자동 의존관계 설정, 자바 코드로 직접 스프링 빈 등록"

categories: [devlog]

tag: javaSpring

toc: true

toc_sticky: true

date: 2021-07-21

last_modified_at: 2021-07-21
---





# 회원관리 예제



# 스프링 빈과 의존관계



#### 스프링 빈을 등록하고, 의존관계 설정하기



**"스프링 빈을 등록하는 2가지 방법"**

* 컴포넌트 스캔[^1]과 자동 의존관계 설정
* 자바 코드로 직접 스프링 빈 등록하기



<컴포넌트 스캔 방식>

```
@Controller
public class MemberController {

    private final MemberService memberService;

    @Autowired
    public MemberController(MemberService memberService) {
        this.memberService = memberService;
    }

}
```




```
@Repository
public class MemoryMemberRepository implements MemberRepository{
```




```
@Service
public class MemberService {

    private final MemberRepository memberRepository;

    @Autowired
    public MemberService(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }
```



##### 컴포넌트 스캔과 자동 의존관계 설정

* @Component 에노테이션이 있으면 스프링 빈으로 자동 등록된다.

* @Controller 컨트롤러가 스프링 빈으로 자동 등록된 이유도 컴포넌트 스캔 때문이다.

* @Component를 포함하는 다음 에노테이션도 스프링 빈으로 자동 등록된다.

  * @Controller

  * @Service

  * @Repository

    

@Autowired 는 관계를 연결해주는것.



<img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/springbean/springbean1.JPG?raw=true">



---

[^1]:@Service, @Controller 등 `@Component`를 가진 모든 대상을 가져와서 빈에 등록하기 위해 찾는 과정



#### 자바 코드로 직접 스프링 빈 등록하기

> 사전 작업
>
> 기존 컴포넌트 삭제
>
> MemberService의 @Service, @Autowired
>
> MemoryMemberRepository 의 @Repository 삭제



main - java - hello.hellospring 아래에 SpringConfig 클래스 생성

```
package hello.hellospring;

import hello.hellospring.repository.MemberRepository;
import hello.hellospring.repository.MemoryMemberRepository;
import hello.hellospring.service.MemberService;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class SpringConfig {

    @Bean
    public MemberService memberService(){
        return new MemberService(memberRepository());
    }

    @Bean
    public MemberRepository memberRepository(){
        return new MemoryMemberRepository();
    }
}
```

* Configuration 설정
  * 빈에 등록할 Memberservice 와 MemberRepository 설정
  * @Bean으로 등록



* MemberController 컴포넌트 스캔으로 스프링 컨테이너에 스프링 빈 등록
* @Autowired 로 memberService를 연결
* 직접 등록한 Memberservice , memberRepository가 연결







---

<참고내용>

* 과거에는 xml로 설정하는 방식도 있었지만, 최근에는 잘 사용하지 않음.

* di의 3가지
  * ```
    public MemberController(MemberService memberService) {
        this.memberService = memberService;
    }
    ```
    
    * 생성자주입
    
  * ```
    @Autowired
    private MemberService memberService;
    ```
    
    * 필드주입
    
  * ```
    public void setMemberService(MemberService memberService) {
        this.memberService = memberService;
    }
    ```
    
    * 세터 주입
    * 세터주입의 단점은 누군가 컨트롤러를 호출했을때 public으로 열려있어야한다.(노출이 되어있음)
    * 변경이 가능함
    
  * 의존관계가 실행중에 동적으로 변하는 경우는 거의 없으므로 생성자 주입을 권장함
  
* 실무에서는 주로 정형화된 컨트롤러, 서비스, 리포지토리  같은 코드는 컴포넌트 스캔을 사용. 정형화되지 않거나, 상황에따라 구현 클래스가 변경해야 하면 설정을 통해 스프린 빈으로 등록

  * 추후 구현체를 바꾼다던지 할때 기존 운영코드를 수정안하고 설정파일에서만 수정해주면 됨

* `@Autowired`를 통한 DI는 `helloController`, `MemberService` 등과 같이 스프링이 관리하는 객체에서만 동작. 스프링 빈으로 등록하지 않고 내가 직접 생성한 객체에서는 동작하지 않음.





### 참조

[김영한님의 인프런강의](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%9E%85%EB%AC%B8-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8)
