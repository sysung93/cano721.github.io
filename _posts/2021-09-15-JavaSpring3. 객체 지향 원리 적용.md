---
title: "3.JavaSpring 객체 지향 원리 적용"
excerpt: "새로운 할인정책 개발, AppConfig 리팩터링, 객제치향설계, IoC,DI,컨테이너"

categories: [devlog]

tag: javaSpring

toc: true

toc_sticky: true

date: 2021-09-15

last_modified_at: 2021-09-15

---



# 스프링 핵심원리 이해1 - 예제 만들기



* 객체지향의 원칙을 준수했는지 확인

* 새로운 정률 할인 적용

  * 구현체 생성(기존 fix -> rate 구현체 생성 후 변경)

    * ```
      package hello.core.discount;
      
      import hello.core.member.Grade;
      import hello.core.member.Member;
      
      public class RateDiscountPolicy implements DiscountPolicy {
      
          private int discountPercent = 10;
      
          @Override
          public int discount(Member member, int price) {
              if (member.getGrade() == Grade.VIP) {
                  return price * discountPercent / 100;
              } else {
                  return 0;
              }
          }
      }
      ```

  * 테스트 생성(ctrl+shift+t)

    * ```
      package hello.core.discount;
      
      import hello.core.member.Grade;
      import hello.core.member.Member;
      import org.assertj.core.api.Assertions;
      import org.junit.jupiter.api.DisplayName;
      import org.junit.jupiter.api.Test;
      
      import static org.assertj.core.api.Assertions.assertThat;
      import static org.junit.jupiter.api.Assertions.*;
      
      class RateDiscountPolicyTest {
      
          RateDiscountPolicy discountPolicy = new RateDiscountPolicy();
      
          @Test
          @DisplayName("VIP는 10% 할인이 적용되어야 한다")
          void vip_o(){
              //given
              Member member = new Member(1l, "memberVIP", Grade.VIP);
              //when
              int discount = discountPolicy.discount(member, 10000);
              //then
              assertThat(discount).isEqualTo(1000);
          }
      
          @Test
          @DisplayName("VIP가 아니면 할인이 적용되지 않아야 한다")
          void vip_x(){
              //given
              Member member = new Member(2l, "memberBASIC", Grade.BASIC);
              //when
              int discount = discountPolicy.discount(member, 10000);
              //then
              assertThat(discount).isEqualTo(0);
          }
      }
      ```

  * 애플리케이션 적용

    * ```
      public class OrderServiceImpl implements OrderService{
      
          private final MemberRepository memberRepository = new MemoryMemberRepository();
      //    private final DiscountPolicy discountPolicy = new FixDiscountPolicy();
          private final DiscountPolicy discountPolicy = new RateDiscountPolicy();
      ```





* **여기서 문제점이 발생함**
  * DIP와 OCP 위반 발생
  * <img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/springCore/springCore7.JPG?raw=true">
  * <img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/springCore/springCore8.JPG?raw=true">
    * 추상체인 인터페이스 뿐만 아니라 구현체도 의존DIP위반
  * <img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/springCore/springCore9.JPG?raw=true">
    * 그로인해 할인정책 변경 시 OrderServiceImpl도 변경 OCP 위반



* **해결방안**

  * 인터페이스만 의존하도록 설계

    * ```
      public class OrderServiceImpl implements OrderService{
      
          private final MemberRepository memberRepository = new MemoryMemberRepository();
      //    private final DiscountPolicy discountPolicy = new FixDiscountPolicy();
      //    private final DiscountPolicy discountPolicy = new RateDiscountPolicy();
          private DiscountPolicy discountPolicy;
      ```

    * 이렇게하면 NPE(null pointer exception) 발생

    * NPE 해결하기 위해선 누가 대신 구현 객체를 생성하고 주입해줘야함(**AppConfig!!**)





* 관심사의 분리
  * 각각의 인터페이스를 배역(배우의 역할)이라고 생각하자! 배역을 선택하는 것은 배우가 아니다!
  * 즉 배우(구현체)가 여주인공 배역(인터페이스)을 직접 초대하고 공연도하고 하는것과 같음. 다양한 책임을 가져버림!
  * 배우(구현체)는 본인의 역할인 배역을 수행하는것만 집중!
  * 그러므로 배역(인터페이스)에 맞는 배우(구현체)를 지정하는 책임을 가지는 "**공연 기획자**"가 있어야함!



* AppConfig 등장

  * 애플리케이션의 전체 동작 방식을 구성(config)하기 위해, **구현객체를 생성**하고, **연결**하는 별도의 설정 클래스를 생성!

  * AppConfig 설정

    * MemberServiceImpl(구현체 의존 제거!)

    * ```
      package hello.core.member;
      
      public class MemberServiceImpl implements MemberService{
      
          private final MemberRepository memberRepository;
      
          public MemberServiceImpl(MemberRepository memberRepository) {
              this.memberRepository = memberRepository;
          }
      
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

    * OrderServiceImpl(구현체 의존 제거!)

    * ```
      package hello.core.order;
      
      import hello.core.discount.DiscountPolicy;
      import hello.core.discount.FixDiscountPolicy;
      import hello.core.discount.RateDiscountPolicy;
      import hello.core.member.Member;
      import hello.core.member.MemberRepository;
      import hello.core.member.MemoryMemberRepository;
      
      public class OrderServiceImpl implements OrderService{
      
          private final MemberRepository memberRepository;
          private final DiscountPolicy discountPolicy;
      
          public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
              this.memberRepository = memberRepository;
              this.discountPolicy = discountPolicy;
          }
      
          @Override
          public Order createOrder(Long memberId, String itemName, int itemPrice) {
              Member member = memberRepository.findById(memberId);
              int discountPrice = discountPolicy.discount(member,itemPrice);
      
              return new Order(memberId, itemName, itemPrice, discountPrice);
          }
      }
      ```

    * AppConfig 클래스 생성( 생성한 객체 인스턴스의 참조(레퍼런스)를 **"생성자를 통해서 주입"**)

    * ```
      package hello.core;
      
      import hello.core.discount.FixDiscountPolicy;
      import hello.core.member.MemberService;
      import hello.core.member.MemberServiceImpl;
      import hello.core.member.MemoryMemberRepository;
      import hello.core.order.OrderService;
      import hello.core.order.OrderServiceImpl;
      
      public class AppConfig {
      
          public MemberService memberService() {
              return new MemberServiceImpl(new MemoryMemberRepository());
          }
      
          public OrderService orderService(){
              return new OrderServiceImpl(new MemoryMemberRepository(),new FixDiscountPolicy());
          }
      
      }
      ```

  * 각 인터페이스내에서 구현체를 연결해주는게 아닌 AppConfig에서 맞는 구현체를 생성자를 통해서 주입(연결)해준다!

  * 이렇게하면 `MemberServiceImpl`은 `MemoryMemberRepository`(구현체)를 의존하지 않는다!
  
  * 단지 `MemberRepository`(추상체) 인터페이스만 의존한다!
  
  * `MemberServiceImpl`입장에선 생성자를 통해 어떤 구현 객체가 들어올지(주입될지)는 알 수 없다.
  
  * 어떤 구현 객체를 주입할지는 오직 외부 `AppConfig`에서 결정!
  
  * 이제부터 **"의존관계에 대한 고민은 외부"**에 맡기고 **"실행에만 집중"**하면 됨
  
  * <img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/springCore/springCore10.JPG?raw=true">
  
    * 객체의 생성과 연결은 `AppConfig`가 담당
    * **DIP(의존관계 역전원칙) 완성**
    * 관심사의 분리: 객체를 생성하고 연결하는 역할과 실행하는 역할이 명확히 분리됨
  
  * <img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/springCore/springCore11.JPG?raw=true">
  
    * `appConfig`객체는 `memoryMemberRepository` 객체를 생성하고 그 참조값을 `memberServiceImpl`을 생성하면서 생성자로 전달
    * 클라이언트인 `memberServiceImpl` 입장에서 보면 의존관계가 마치 외부에서 주입해주는 것 같다고 해서 **DI(Dependency Injection)** 우리말로 의존관계 주입 또는 의존성 주입이라고 한다!
  
  * 테스트 코드 변경
  
    * ```
      package hello.core.member;
      
      import hello.core.AppConfig;
      import org.assertj.core.api.Assertions;
      import org.junit.jupiter.api.BeforeEach;
      import org.junit.jupiter.api.Test;
      
      public class MemberServiceTest {
      
          MemberService memberService;
      
          @BeforeEach
          public void beforeEach(){
              AppConfig appConfig = new AppConfig();
              memberService = appConfig.memberService();
          }
      
          @Test
          void join(){
              //given
              Member member = new Member(1l,"memeberA",Grade.VIP);
      
              //when
              memberService.join(member);
              Member findMember = memberService.findMember(1l);
      
              //then
              Assertions.assertThat(member).isEqualTo(findMember);
      
          }
      }
      ```
  
    * ```
      package hello.core.order;
      
      import hello.core.AppConfig;
      import hello.core.member.Grade;
      import hello.core.member.Member;
      import hello.core.member.MemberService;
      import org.assertj.core.api.Assertions;
      import org.junit.jupiter.api.BeforeEach;
      import org.junit.jupiter.api.Test;
      
      public class OrderServiceTest {
      
          MemberService memberService;
          OrderService orderService;
      
          @BeforeEach
          public void beforeEach(){
              AppConfig appConfig = new AppConfig();
              memberService = appConfig.memberService();
              orderService = appConfig.orderService();
          }
      
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
  
    * `@BeforeEach` 은 각 테스트를 실행하기 전에 호출된다.















### 참조

[김영한님의 인프런강의](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8)

