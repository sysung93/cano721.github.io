---
title: "2.JavaSpring 비즈니스 요구사항과 설계"
excerpt: "회원,주문,할인 도메인 실행 및 테스트"

categories: [devlog]

tag: javaSpring

toc: true

toc_sticky: true

date: 2021-08-24

last_modified_at: 2021-08-28

---



# 스프링 핵심원리 이해1 - 예제 만들기

> **<사전 준비물>**
>
> JAVA 11 설치(사전에 다른 버전이 설치되어있다면 11로 변경 -> 변경 실패 시 자바 다 삭제했다가 새로 까는게 편함..)
>
> IntelliJ 설치
>
> https://start.spring.io/ 를 통한 프로젝트 생성



* 빌드 설정

  * ```
    plugins {
       id 'org.springframework.boot' version '2.3.3.RELEASE'
       id 'io.spring.dependency-management' version '1.0.9.RELEASE'
       id 'java'
    }
    group = 'hello'
    version = '0.0.1-SNAPSHOT'
    sourceCompatibility = '11'
    repositories {
       mavenCentral()
    }
    dependencies {
       implementation 'org.springframework.boot:spring-boot-starter'
       testImplementation('org.springframework.boot:spring-boot-starter-test') {
          exclude group: 'org.junit.vintage', module: 'junit-vintage-engine'
       }
    }
    test {
       useJUnitPlatform()
    }
    ```





### 비즈니스 요구사항과 설계





**인터페이스 기반으로 설계하여 구현체 변경 용이하게 개발하자!**



---

### 회원관련 코드



* 회원
  * 회원을 가입, 조회
  * 등급: 일반,VIP
  * 데이터: 자체 DB 구축 or 외부 시스템 연동 가능(미확정)



<img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/springCore/springCore1.JPG?raw=true">

#### 회원 객체 다이어그램

<img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/springCore/springCore2.JPG?raw=true">



* Member 클래스 생성

  * ```
    package hello.core.member;
    
    public class Member {
    
        private Long id;
        private String name;
        private Grade grade;
    
        public Member(Long id, String name, Grade grade) {
            this.id = id;
            this.name = name;
            this.grade = grade;
        }
    
        public Long getId() {
            return id;
        }
    
        public void setId(Long id) {
            this.id = id;
        }
    
        public String getName() {
            return name;
        }
    
        public void setName(String name) {
            this.name = name;
        }
    
        public Grade getGrade() {
            return grade;
        }
    
        public void setGrade(Grade grade) {
            this.grade = grade;
        }
    }
    ```

* Grade enum 생성

  * ```
    package hello.core.member;
    
    public enum Grade {
        BASIC,
        VIP
    }
    ```

* Repository 생성

  * 인터페이스

    * ```
      package hello.core.member;
      
      public interface MemberRepository {
      
          void save(Member member);
      
          Member findById(Long memberId);
      
      }
      ```

  * 구현체

    * ```
      package hello.core.member;
      
      import java.util.HashMap;
      import java.util.Map;
      
      public class MemoryMemberRepository implements MemberRepository{
      
          // 동시성 이슈가 생길 수 있다. -> 추후엔 컨커런트 해쉬맵? 이란걸 써야함
          private static Map<Long, Member> store = new HashMap<>();
      
          @Override
          public void save(Member member) {
              store.put(member.getId(), member);
          }
      
          @Override
          public Member findById(Long memberId) {
              return store.get(memberId);
          }
      }
      ```

* Service 생성

  * 인터페이스

    * ```
      package hello.core.member;
      
      public interface MemberService {
      
          void join(Member member);
      
          Member findMember(Long memberId);
      }
      ```

  * 구현체

    * ```
      package hello.core.member;
      
      public class MemberServiceImpl implements MemberService{
      	// dip 위반 : 추상체인 인터페이스와 구현체인 클래스를 동시에 의존
          private final MemberRepository memberRepository = new MemoryMemberRepository();
      
          @Override
          public void join(Member member) {
              memberRepository.save(member);
          }
      
          @Override
          public Member findMember(Long memberId) {
              return memberRepository.findById(memberId);
          }
      }
      ```

* 작동 앱

  * ```
    package hello.core;
    
    import hello.core.member.Grade;
    import hello.core.member.Member;
    import hello.core.member.MemberService;
    import hello.core.member.MemberServiceImpl;
    
    public class MemberApp {
    
        public static void main(String[] args) {
            MemberService memberService = new MemberServiceImpl();
            Member member = new Member(1l, "memeberA", Grade.VIP);
            memberService.join(member);
    
            Member findMember = memberService.findMember(1l);
            System.out.println("new member = " + member.getName());
            System.out.println("findMember = " + findMember.getName());
        }
    }
    ```

* 테스트 코드

  * ```
    package hello.core.member;
    
    import org.assertj.core.api.Assertions;
    import org.junit.jupiter.api.Test;
    
    public class MemberServiceTest {
        MemberService memberService = new MemberServiceImpl();
        @Test
        void join(){
            //given
            Member member = new Member(1l,"memeberA",Grade.VIP);
    
            //when
            memberService.join(member);
            Member findMember = memberService.findMember(2l);
    
            //then
            Assertions.assertThat(member).isEqualTo(findMember);
    
        }
    }
    ```





---

### 주문과 할인 관련 코드



* 주문과 할인 정책
  * 회원은 상품 주문 가능
  * 회원 등급에따른 할인 정책 적용
  * 할인 정책은 모든 VIP는 1000원을 할인해주는 고정 금액 할인 적용
  * 할인 정책은 변경 가능성 높음



<img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/springCore/springCore3.JPG?raw=true">

* 주문 도메인 전체

<img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/springCore/springCore4.JPG?raw=true">

* 주문 도메인 클래스 다이어그램

<img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/springCore/springCore5.JPG?raw=true">

<img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/springCore/springCore6.JPG?raw=true">



* 주문 클래스 생성

  * ```
    package hello.core.order;
    
    public class Order {
    
        private Long memberId;
        private String itemName;
        private int itemPrice;
        private int discountPrice;
    
        public Order(Long memberId, String itemName, int itemPrice, int discountPrice) {
            this.memberId = memberId;
            this.itemName = itemName;
            this.itemPrice = itemPrice;
            this.discountPrice = discountPrice;
        }
    
        //계산로직
        public int calculatePrice(){
            return itemPrice - discountPrice;
        }
    
        public Long getMemberId() {
            return memberId;
        }
    
        public void setMemberId(Long memberId) {
            this.memberId = memberId;
        }
    
        public String getItemName() {
            return itemName;
        }
    
        public void setItemName(String itemName) {
            this.itemName = itemName;
        }
    
        public int getItemPrice() {
            return itemPrice;
        }
    
        public void setItemPrice(int itemPrice) {
            this.itemPrice = itemPrice;
        }
    
        public int getDiscountPrice() {
            return discountPrice;
        }
    
        public void setDiscountPrice(int discountPrice) {
            this.discountPrice = discountPrice;
        }
    
        @Override
        public String toString() {
            return "Order{" +
                    "memberId=" + memberId +
                    ", itemName='" + itemName + '\'' +
                    ", itemPrice=" + itemPrice +
                    ", discountPrice=" + discountPrice +
                    '}';
        }
    }
    ```

* 주문서비스 생성

  * 인터페이스

    * ```
      package hello.core.order;
      
      public interface OrderService {
          Order createOrder(Long memberId, String itemName, int itemPrice);
      
      }
      ```

  * 구현체

    * ```
      package hello.core.order;
      
      import hello.core.discount.DiscountPolicy;
      import hello.core.discount.FixDiscountPolicy;
      import hello.core.member.Member;
      import hello.core.member.MemberRepository;
      import hello.core.member.MemoryMemberRepository;
      
      public class OrderServiceImpl implements OrderService{
      
          private final MemberRepository memberRepository = new MemoryMemberRepository();
          private final DiscountPolicy discountPolicy = new FixDiscountPolicy();
      
          @Override
          public Order createOrder(Long memberId, String itemName, int itemPrice) {
              Member member = memberRepository.findById(memberId);
              int discountPrice = discountPolicy.discount(member,itemPrice);
      
              return new Order(memberId, itemName, itemPrice, discountPrice);
          }
      }
      ```

* 할인

  * 인터페이스

    * ```
      package hello.core.discount;
      
      import hello.core.member.Member;
      
      public interface DiscountPolicy {
      
          /*
             @return 할인 대상 금액액
          */
          int discount(Member member, int price);
      
      }
      ```

  * 구현체

    * ```
      package hello.core.discount;
      
      import hello.core.member.Grade;
      import hello.core.member.Member;
      
      public class FixDiscountPolicy implements DiscountPolicy{
      
          private int discountFixAmount = 1000; //1000원 할인
      
          @Override
          public int discount(Member member, int price) {
              if (member.getGrade() == Grade.VIP) {
                  return discountFixAmount;
              } else {
      
                  return 0;
              }
          }
      }
      ```



* 작동 앱

  * ```
    package hello.core;
    
    import hello.core.member.Grade;
    import hello.core.member.Member;
    import hello.core.member.MemberService;
    import hello.core.member.MemberServiceImpl;
    import hello.core.order.Order;
    import hello.core.order.OrderService;
    import hello.core.order.OrderServiceImpl;
    
    public class OrderApp {
    
        public static void main(String[] args) {
            MemberService memberService = new MemberServiceImpl();
            OrderService orderService = new OrderServiceImpl();
    
            Long memberId = 1l;
            Member member = new Member(memberId, "memberA", Grade.VIP);
            memberService.join(member);
    
            Order order = orderService.createOrder(memberId, "itemA", 10000);
    
            System.out.println("order = " + order.toString());
        }
    }
    ```



* 테스트코드

  * ```
    package hello.core.order;
    
    import hello.core.member.Grade;
    import hello.core.member.Member;
    import hello.core.member.MemberService;
    import hello.core.member.MemberServiceImpl;
    import org.assertj.core.api.Assertions;
    import org.junit.jupiter.api.Test;
    
    public class OrderServiceTest {
    
        MemberService memberService = new MemberServiceImpl();
        OrderService orderService = new OrderServiceImpl();
    
        @Test
        void createOrder(){
            Long memberId = 1l;
            Member member = new Member(memberId, "memberA", Grade.VIP);
    
            memberService.join(member);
    
            Order order = orderService.createOrder(memberId, "itemA", 10000);
            Assertions.assertThat(order.getDiscountPrice()).isEqualTo(1000);
        }
    }
    ```





* 정리
  * 인터페이스를 분리하고 구현체를 생성하여 다형성을 잘 적용한거같지만, DIP 의존관계 역전 원칙은 잘 안지켜진것으로 보임..
    * EX) public class MemberServiceImpl implements MemberService{
      	// dip 위반 : 추상체인 인터페이스와 구현체인 클래스를 동시에 의존
          private final MemberRepository memberRepository = new MemoryMemberRepository();
  * 추후 강의에서 DIP 의존관계를 지키기위해 수정





### 참조

[김영한님의 인프런강의](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8)

