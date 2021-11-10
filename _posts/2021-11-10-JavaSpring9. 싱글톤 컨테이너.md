---
title: "9. 싱글톤 컨테이너"
excerpt: ""

categories: [devlog]

tag: javaSpring

toc: true

toc_sticky: true

date: 2021-11-10

last_modified_at: 2021-11-10

---



# 싱글톤 컨테이너



### 웹 애플리케이션과 싱글톤

* 스프링은 태생이 기업용 온라인 서비스 기술을 지원하기 위해 탄생했다.
* 대부분의 스프링 애플리케이션은 웹 애플리케이션이다. 물론 웹이 아닌 애플리케이션 개발도 얼마든지 개발할 수 있다.
* 웹 애플리케이션은 보통 여러 고객이 동시에 요청을 한다.



<img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/springCore/springCore27.JPG?raw=true">

* 스프링 없는 순수한 DI 컨테이너 테스트

  * ```
    package hello.core.singleton;
    
    import hello.core.AppConfig;
    import hello.core.member.Member;
    import hello.core.member.MemberService;
    import org.assertj.core.api.Assertions;
    import org.junit.jupiter.api.DisplayName;
    import org.junit.jupiter.api.Test;
    
    public class SingletonTest {
    
        @Test
        @DisplayName("스프링 없는 순수한 DI 컨테이너")
        void pureContatiner(){
            AppConfig appConfig = new AppConfig();
            //1. 조회: 호출할 때 마다 객체를 생성
            MemberService memberService1 = appConfig.memberService();
    
            //2. 조회: 호출할 때 마다 객체를 생성
            MemberService memberService2 = appConfig.memberService();
    
            //참조값이 다른 것을 확인
            System.out.println("memberService1 = " + memberService1);
            System.out.println("memberService2 = " + memberService2);
    
            //memberService1 != memberService2
            Assertions.assertThat(memberService1).isNotSameAs(memberService2);
        }
    }
    ```



* 우리가 만들었던 스프링 없는 순수한 DI 컨테이너인 AppConfig는 요청을 할 때 마다 객체를 새로 생성한다.
* 고객 트래픽이 초당 100이 나오면 초당 100개 객체가 생성되고 소멸된다! -> 메모리 낭비가 심하다.
* 해결방안은 해당 객체가 딱 1개만 생성되고, 공유하도록 설계하면 된다. -> **싱글톤 패턴!**



### 싱글톤 패턴

* 클래스의 인스턴스가 딱 1개만 생성되는 것을 보장하는 디자인 패턴이다.
* 그래서 객체 인스턴스를 2개 이상 생성하지 못하도록 막아야 한다.
  * private 생성자를 사용해서 외부에서 임의로 new 키워드를 사용하지 못하도록 막아야 한다.



싱글톤 패턴을 적용한 예제 코드 ( "main이 아닌 Test쪽에 작성")

* ```
  package hello.core.singleton;
  
  public class SingleTonService {
  
      private static final SingleTonService instance = new SingleTonService();
  
      public static SingleTonService getInstance( ){
          return instance;
      }
  
      private SingleTonService(){
      }
  
      public void logic(){
          System.out.println("싱글톤 객체 로직 호출");
      }
  }
  ```

* 1. static 영역에 객체 instance를 미리 하나 생성해서 올려둔다.
  2. 이 객체 인스턴스가 필요하면 오직 `getInstance()` 메서드를 통해서만 조회할 수 있다. 이 메서드를 호출하면 항상 같은 인스턴스를 반환한다.
  3. 딱 1개의 객체 인스턴스만 존재해야 하므로, 생성자를 private으로 막아서 혹시라도 외부에서 new 키워드로 객체 인스턴스가 생성되는 것을 막는다.



싱글톤 패턴 적용 테스트 코드

* ```
  @Test
  @DisplayName("싱글톤 패턴을 적용한 객체 사용")
  void singletonServiceTest(){
      SingleTonService singletonService1 = SingleTonService.getInstance();
      SingleTonService singletonService2 = SingleTonService.getInstance();
  
      System.out.println("singletonService1 = " + singletonService1);
      System.out.println("singletonService2 = " + singletonService2);
  
      assertThat(singletonService1).isSameAs(singletonService2);
  }
  ```

* private으로 new 키워드는 막아두었다.

* 호출할 때 마다 같은 객체 인스턴스를 반환



| 참고: 싱글톤 패턴을 구현하는 방법은 여러가지 있다. 여기서는 객체를 미리 생성해두는 가장 단순하고 안전한 방법을 선택



**싱글톤 패턴의 장점**

* 이미 만들어진 객체를 공유해서 효율적으로 사용 가능

**싱글톤 패턴의 문제점**

* 싱글톤 패턴을 구현하는 코드 자체가 많이 들어간다(instance, private 생성자.. 등등)
* 의존관계상 클라이언트가 구체 클래스에 의존한다 -> DIP 위반, OCP 위반 가능성 높음.
* 테스트하기 어렵다.
* 내부 속성을 변경하거나 초기화 하기 어렵다
* private 생성자로 자식 클래스를 만들기 어렵다.
* 결론적으로 유연성이 떨어진다.
* 안티패턴으로 불리기도 한다.



### 싱글톤 컨테이너(스프링 컨테이너)

스프링 컨테이너는 싱글톤 패턴의 문제점을 해결하면서, 객체 인스턴스를 싱글톤(1개만 생성)으로 관리한다.

스프링빈이 바로 싱글톤으로 관리되는 빈이다.



**싱글톤 컨테이너**

* 스프링 컨테이너는 싱글톤 패턴을 적용하지 않아도, 객체 인스턴스를 싱글톤으로 관리
  * 컨테이너는 객체를 하나만 생성해서 관리
* 스프링 컨테이너는 싱글톤 컨테이너 역할을 한다. 이렇게 싱글톤 객체를 생성하고 관리하는 기능을 싱글톤 레지스트리라 한다.
* 스프링 컨테이너의 이런 기능 덕분에 싱글톤 패턴의 모든 단점을 해결하면서 객체를 싱글톤으로 유지할 수 있다.
  * 싱글톤 패턴을 위한 지전분한 코드가 들어가지 않아도 된다.
  * DIP,OCP,테스트,private 생성자로부터 자유롭게 싱글톤 사용 가능하다.



"스프링 컨테이너를 사용하는 테스트 코드"

* ```
  @Test
  @DisplayName("스프링 컨테이너와 싱글톤")
  void springContainer(){
  
      //AppConfig appConfig = new AppConfig();
      ApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
      //1. 조회: 호출할 때 마다 객체를 생성
      MemberService memberService1 = ac.getBean("memberService",MemberService.class);
      MemberService memberService2 = ac.getBean("memberService",MemberService.class);
  
      //참조값이 다른 것을 확인
      System.out.println("memberService1 = " + memberService1);
      System.out.println("memberService2 = " + memberService2);
  
      //memberService1 != memberService2
      assertThat(memberService1).isSameAs(memberService2);
  }
  ```

* <img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/springCore/springCore28.JPG?raw=true">

* 스프링 컨테이너 덕분에 고객의 요청이 올 때 마다 객체를 생성하는 것이 아니라, 이미 만들어진 객체를 공유해서 효율적으로 재사용할 수 있다.



|참고: 스프링의 기본 빈 등록 방식은 싱글톤이지만, 싱글톤 방식만 지원하는 것은 아니다. 요청할 때 마다 새로운 객체를 생성해서 반환하는 기능도 제공한다. 자세한 내용은 빈 스코프에서 설명함.



### 싱글톤 방식의 주의점

* 객체 인스턴스를 하나만 생성해서 공유하는 싱글톤 방식은 여러 클라이언트가 하나의 같은 객체 인스턴스를 공유하기 때문에 싱글톤 객체는 상태를 유지(stateful)하게 설계하면 안된다.
* 무상태(stateless)로 설계해야 한다
  * 특정 클라이언트에 의존적인 필드가 있으면 안된다.
  * 특정 클라이언트가 값을 변경할 수 있는 필드가 있으면 안된다.
  * 가급적 읽기만 가능해야 한다.
  * 필드 대신에 자바에서 공유되지 않는, 지역변수, 파라미터, ThreadLocal 등을 사용해야 한다.
* 스프링 빈의 필드에 공유 값을 설정하면 정말 큰 장애가 발생할 수 있다!



문제점 예시 코드

* ```
  package hello.core.singleton;
  
  import org.assertj.core.api.Assertions;
  import org.junit.jupiter.api.Test;
  import org.springframework.context.ApplicationContext;
  import org.springframework.context.annotation.AnnotationConfigApplicationContext;
  import org.springframework.context.annotation.Bean;
  
  import static org.junit.jupiter.api.Assertions.*;
  
  class StatefulServiceTest {
  
      @Test
      void statefulServiceSingleton(){
          ApplicationContext ac = new AnnotationConfigApplicationContext(TestConfig.class);
          StatefulService statefulService1 = ac.getBean(StatefulService.class);
          StatefulService statefulService2 = ac.getBean(StatefulService.class);
  
          //ThreadA : A사용자 10000원 주문
          statefulService1.order("userA",10000);
          //ThreadA : B사용자 20000원 주문
          statefulService2.order("userB",20000);
  
          //ThreadA: 사용자A 주문 금액 조회
          int price = statefulService1.getPrice();
          System.out.println("price = " + price);
  
          Assertions.assertThat(statefulService1.getPrice()).isEqualTo(20000);
      }
  
      static class TestConfig{
  
          @Bean
          public StatefulService statefulService(){
              return new StatefulService();
          }
      }
  }
  ```

* 최대한 단순한 설명을 위해, 실제 쓰레드는 사용하지 않음.
* ThreadA가 사용자A코드를 호출하고 ThreadB가 사용자 B 코드를 호출한다 가정.
* `StatefulService`의 `price`필드는 공유되는 필드인데, 특정 클라이언트가 값을 변경한다.
* 사용자A의 주문금액은 10000원이 되어야 하는데, 20000원이라는 결과가 나온다.
* 공유필드는 조심! 스프링 빈은 항상 무상태(stateless)로 설계하자!





### @Configuration 과 싱글톤

* 코드

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
    
        // @Bean memberService -> new MemoryMemberRepository()
        // @Bean orderService -> new MemoryMemberRepository()
    
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

* memberService 빈을 만드는 코드와 orderService 빈을 만드는 코드를 통해 두번의 `new MemoryMemberRepository()` 를 호출한다.

* 싱글톤이 깨지는것처럼 보이는데 이 문제를 스프링 컨테이너에서 해결해준다.

* 확인 코드

  * MemberServiceImpl

    * ```
      // 테스트용
      public MemberRepository getMemberRepository() {
          return memberRepository;
      }
      ```

  * OrderServiceImpl

    * ```
      // 테스트 용
      public MemberRepository getMemberRepository() {
          return memberRepository;
      }
      ```

  * ```
    package hello.core.singleton;
    
    import hello.core.AppConfig;
    import hello.core.member.MemberRepository;
    import hello.core.member.MemberServiceImpl;
    import hello.core.order.OrderServiceImpl;
    import org.assertj.core.api.Assertions;
    import org.junit.jupiter.api.Test;
    import org.springframework.context.ApplicationContext;
    import org.springframework.context.annotation.AnnotationConfigApplicationContext;
    
    import static org.assertj.core.api.Assertions.*;
    
    public class ConfigurationSingletonTest {
    
        @Test
        void configurationTest(){
            ApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
    
            MemberServiceImpl memberService = ac.getBean("memberService", MemberServiceImpl.class);
            OrderServiceImpl orderService = ac.getBean("orderService", OrderServiceImpl.class);
            MemberRepository memberRepository = ac.getBean("memberRepository", MemberRepository.class);
    
            MemberRepository memberRepository1 = memberService.getMemberRepository();
            MemberRepository memberRepository2 = orderService.getMemberRepository();
    
            System.out.println("memberService -> memberRepository = " + memberRepository1);
            System.out.println("orderService -> memberRepository = " + memberRepository2);
            System.out.println("memberRepository = " + memberRepository);
    
            assertThat(memberService.getMemberRepository()).isSameAs(memberRepository);
            assertThat(orderService.getMemberRepository()).isSameAs(memberRepository);
        }
    }
    ```

* 확인해보면 memberRepository 인스턴스는 모두 같은 인스턴스가 공유되어 사용된다.



### @Configuration과 바이트코드 조작의 마법

스프링 컨테이너는 싱글톤 레지스트리다. 따라서 스프링 빈이 싱글톤이 되도록 보장해주어야 한다. 그런데 스프링이 자바코드까지 어떻게 하기는 어렵다. 위의 자바 코드를 보면 분명 3번 호출되어야 하지만, 스프링은 클래스의 바이트코드를 조작하는 라이브러리를 사용한다.

모든 비밀은 `@Configuration`을 적용한 `AppConfig`에 있다.

```
@Test
void configurationDeep(){
    ApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
    AppConfig bean = ac.getBean(AppConfig.class);
    System.out.println("bean = " + bean.getClass());
    // 출력: bean = class hello.core.AppConfig$$EnhancerBySpringCGLIB$$a3bb3ed7
}
```

* `AnnotationConfigApplicationContext`에 파라미터로 넘긴 값은 스프링 빈으로 등록된다.
* `AppConfig` 스프링 빈을 조회해서 클래스 정보를 출력
  * bean = class hello.core.AppConfig$$EnhancerBySpringCGLIB$$a3bb3ed7



순수한 클래스라면 다음과 같이 출력되어야한다.

* class hello.core.AppConfig



그런데 예상과 다르게 클래스 명 뒤에 xxxCGLIB가 붙으면서 상당히 복잡해진 것을 볼 수 있다. 이것은 내가 만든 클래스가 아니라 스프링이 CGLIB라는 바이트코드 조작 라이브러리를 사용해서 AppConfig클래스를 상속받은 임의의 다른 클래스를 만들고, 그 다른 클래스를 스프링 빈으로 등록한 것이다!

<img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/springCore/springCore29.JPG?raw=true">

* 임의의 다른 클래스가 바로 싱글톤이 보장되도록 해준다. 아마도 다음과 같이 바이트 코드를 조작해서 작성되어 있을 것이다.(실제로는 CGLIB의 내부 기술을 사용하는데 매우 복잡하다.)

* 예상코드

  * ```
    @Bean
    public MemberRepository memberRepository() {
    
        if (memoryMemberRepository가 이미 스프링 컨테이너에 등록되어 있으면?) {
            return 스프링 컨테이너에서 찾아서 반환;
        } else { //스프링 컨테이너에 없으면
            기존 로직을 호출해서 MemoryMemberRepository를 생성하고 스프링 컨테이너에 등록
            return 반환
        }
    }
    ```

|참고: AppConfig@CGLIB은 AppConfig의 자식 타입이므로, AppConfig 타입으로 조회 할 수 있다.



`@Configuration`을 적용하지 않고, `Bean`만 적용하면 어떻게 될까?

주석처리하고 실행해보면 순수한 클래스만 출력된다.

* bean = class hello.core.AppConfig



@Bean만 사용해도 스프링 빈으로 등록되지만, 싱글톤을 보장하지 않는다. memberRepository() 처럼 의존관계 주입이 필요해서 메서드를 직접 호출할 때 싱글톤을 보장하지 않는다. 크게 고민할 것이 없다. 스프링 설정 정보는 항상 @Configuration 을 사용하자.



### 참조

[김영한님의 인프런강의](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8)

