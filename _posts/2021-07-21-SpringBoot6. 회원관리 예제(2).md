---

title: "6.SpringBoot 회원관리 예제"
excerpt: "회원 서비스 개발, 회원 서비스 테스트"

categories: [devlog]

tag: javaSpring

toc: true

toc_sticky: true

date: 2021-07-21

last_modified_at: 2021-07-21
---





# 회원관리 예제



#### 회원 서비스 개발

<img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/users/users6.JPG?raw=true">



* 패키지 생성 및 클래스 생성



```
package hello.hellospring.service;

import hello.hellospring.domain.Member;
import hello.hellospring.repository.MemberRepository;
import hello.hellospring.repository.MemoryMemberRepository;

import java.util.List;
import java.util.Optional;

public class MemberService {

    private final MemberRepository memberRepository = new MemoryMemberRepository();

    /**
     * 회원가입
     */
    public  Long join(Member member){
        //같은 이름이 있는 중복 회원x
//        Optional<Member> result = memberRepository.findByName(member.getName());
        //ifPresent는 값이 존재하냐
//        result.ifPresent(m -> {
//            throw new IllegalStateException("이미 존재하는 회원입니다.");
//        });

        validateDuplicateMember(member); // 중복회원 검증
        memberRepository.save(member);
        return member.getId();
    }

    private void validateDuplicateMember(Member member) {
        memberRepository.findByName(member.getName())
                .ifPresent(m -> {
                    throw new IllegalStateException("이미 존재하는 회원입니다.");
                });
    }


    /*
        전체 회원 조회
     */
    public List<Member> findMembers(){
        return memberRepository.findAll();
    }

    public Optional<Member> findOne(Long memberId){
        return memberRepository.findById(memberId);
    }
}
```



* join : 회원가입 시 저장 메소드
  * 중복확인을 위한 코드
    * validateDuplicateMember(member); // 중복회원 검증
    * ifPresent 으로 데이터 존재여부 확인
    * 존재 시 Exception 처리
      * throw new IllegalStateException("이미 존재하는 회원입니다."); 처리
* findMembers : 전체 회원 리턴
* findOne : id값에 해당하는 회원 리턴



#### 회원 서비스 테스트



<img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/users/users7.JPG?raw=true">



* ctrl + shift + t를 통해 create Test 생성
* 위의 사진처럼 테스트 만들 메소드들을 클릭 후 확인



```
public class MemberService {

    private final MemberRepository memberRepository;

    public MemberService(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }
```

```
package hello.hellospring.service;

import hello.hellospring.domain.Member;
import hello.hellospring.repository.MemoryMemberRepository;
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

import static org.assertj.core.api.Assertions.assertThat;
import static org.junit.jupiter.api.Assertions.*;

class MemberServiceTest {

    MemberService memberService = new MemberService();
    MemoryMemberRepository memberRepository;

    @BeforeEach
    public void beforeEach(){
        memberRepository = new MemoryMemberRepository();
        memberService = new MemberService(memberRepository);
    }

    @AfterEach
    public void afterEach(){
        memberRepository.clearStore();
    }


    @Test
    void 회원가입() {
        //given
        Member member = new Member();
        member.setName("hello");
        
        //when
        Long saveId = memberService.join(member);

        //then
        Member findMember = memberService.findOne(saveId).get();
        assertThat(member.getName()).isEqualTo(findMember.getName());
    }

    @Test
    public void 중복_회원_예외(){
        //given
        Member member1 = new Member();
        member1.setName("spring");

        Member member2 = new Member();
        member2.setName("spring");

        //when
        memberService.join(member1);
        IllegalStateException e = assertThrows(IllegalStateException.class, () -> memberService.join(member2));

        assertThat(e.getMessage()).isEqualTo("이미 존재하는 회원입니다.");

//        try {
//            memberService.join(member2);
//            fail();
//        } catch (IllegalStateException e){
//            assertThat(e.getMessage()).isEqualTo("이미 존재하는 회원입니다.");
//        }

        //then
    }


    @Test
    void findMembers() {
    }

    @Test
    void findOne() {
    }
}
```

* 테스트코드는 직관적으로 한글로 해도 됨(외국인이랑 일할거 아닌이상..)
* MemberService와 테스트의 repository를 동일하게하기위해 di[^1]설정
  * MemberService에서 외부에서 매개변수로 넣어주도록 설정
    * public MemberService(MemberRepository memberRepository) {
              this.memberRepository = memberRepository;
          }
  * MemberServiceTest에서 @BeforeEach로 동작전에 repository를 넣도록 설정
    * @BeforeEach
          public void beforeEach(){
              memberRepository = new MemoryMemberRepository();
              memberService = new MemberService(memberRepository);
          }



* 회원가입: 회원가입 시 테스트

* 중복회원예외 : 중복회원 가입 시 처리

  





---

[^1]:di(defendency injection) 의존성 주입. 여기서 di를 한 이유는 public class MemoryMemberRepository 에서 **static**  Map<Long, Member> store 으로 선언했기때문에 모든 인스턴스에서 단 하나의 Map만 사용하지만, static을 제거하게되면 각각의 인스턴스가 자기만의 Map을 가지게 되므로 di설정을 했다.



### 참조

[김영한님의 인프런강의](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%9E%85%EB%AC%B8-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8)
