---
title: "5. 스프링으로 전환하기"
excerpt: ""

categories: [devlog]

tag: javaSpring

toc: true

toc_sticky: true

date: 2021-11-08

last_modified_at: 2021-11-08

---



# IoC, DI , 그리고 컨테이너



# 스프링으로 전환하기



지금까지 순수한 자바 코드만으로 DI를 적용한것. 이제 스프잉을 사용해서 적용할것!



* AppConfig

  * AppConfig에 설정을 구성한다는 뜻인 `@Configuration` 어노테이션을 붙여줌.

  * 각 메서드에 `@Bean`을 붙여 스프링 컨테이너에 스프링 빈으로 등록한다.

  * ```
    package hello.core;
    
    import hello.core.discount.DiscountPolicy;
    import hello.core.discount.FixDiscountPolicy;
    import hello.core.discount.RateDiscountPolicy;
    import hello.core.member.MemberRepository;
    import hello.core.member.MemberService;
    import hello.core.member.MemberServiceImpl;
    import hello.core.member.MemoryMemberRepository;
    import hello.core.order.OrderService;
    import hello.core.order.OrderServiceImpl;
    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;
    
    @Configuration
    public class AppConfig {
    
        @Bean
        public MemberService memberService() {
    
            return new MemberServiceImpl(memberRepository());
        }
    
        @Bean
        public OrderService orderService(){
    
            return new OrderServiceImpl(memberRepository(),discountPolicy());
        }
    
        @Bean
        public MemberRepository memberRepository() {
    
            return new MemoryMemberRepository();
        }
    
        @Bean
        public DiscountPolicy discountPolicy(){
            //return new FixDiscountPolicy();
            return new RateDiscountPolicy();
        }
    }
    ```

* MemberApp

  * ```
    package hello.core;
    
    import hello.core.member.Grade;
    import hello.core.member.Member;
    import hello.core.member.MemberService;
    import hello.core.member.MemberServiceImpl;
    import org.springframework.context.ApplicationContext;
    import org.springframework.context.annotation.AnnotationConfigApplicationContext;
    
    public class MemberApp {
    
        public static void main(String[] args) {
    //        AppConfig appConfig = new AppConfig();
    //        MemberService memberService = appConfig.memberService();
    
            ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
            MemberService memberService = applicationContext.getBean("memberService", MemberService.class);
    
            Member member = new Member(1l, "memeberA", Grade.VIP);
            memberService.join(member);
    
            Member findMember = memberService.findMember(1l);
            System.out.println("new member = " + member.getName());
            System.out.println("findMember = " + findMember.getName());
        }
    }
    ```

* OrderApp

  * ```
    package hello.core;
    
    import hello.core.member.Grade;
    import hello.core.member.Member;
    import hello.core.member.MemberService;
    import hello.core.member.MemberServiceImpl;
    import hello.core.order.Order;
    import hello.core.order.OrderService;
    import hello.core.order.OrderServiceImpl;
    import org.springframework.context.ApplicationContext;
    import org.springframework.context.annotation.AnnotationConfigApplicationContext;
    
    public class OrderApp {
    
        public static void main(String[] args) {
    
    //        AppConfig appConfig = new AppConfig();
    //        MemberService memberService = appConfig.memberService();
    //        OrderService orderService = appConfig.orderService();
    
            ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
    
            MemberService memberService = applicationContext.getBean("memberService",MemberService.class);
            OrderService orderService = applicationContext.getBean("orderService", OrderService.class);
    
            Long memberId = 1l;
            Member member = new Member(memberId, "memberA", Grade.VIP);
            memberService.join(member);
    
            Order order = orderService.createOrder(memberId, "itemA", 20000);
    
            System.out.println("order = " + order.toString());
        }
    }
    ```



**스프링 컨테이너**

* `ApplicationContext`를 스프링 컨테이너라 한다.
* 기존에는 개발자가 `AppConfig`를 사용해서 직접 객체를 생성하고 DI를 했지만, 이제부터는 스프링 컨테이너를 통해서 사용한다.
* `Configuration`이 붙은 `AppConfig`를 설정정보로 사용하고, `@Bean`이라 적힌 메서드를 모두 호출해서 반환된 객체를 스프링 컨테이너에 등록한다. 이렇게 스프링 컨테이너에 등록된 객체를 스프링 빈이라고 한다.
* 스프링 빈은 `@Bean` 어노테이션이 붙은 메서드의 명을 스프링 빈의 이름으로 사용한다.
* 이전에는 필요한 객체를 AppConfig를 사용해서 직접 조회했지만, 이제는 스프링 컨테이너를 통해서 필요한 스프링 빈을 찾아야한다. 스프링 빈은 `applicationContext.getBean()`메서드를 사용해서 찾을 수 있다.











### 참조

[김영한님의 인프런강의](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8)

