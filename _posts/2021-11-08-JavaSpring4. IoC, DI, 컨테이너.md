---
title: "4.JavaSpring IoC, DI, 컨테이너"
excerpt: ""

categories: [devlog]

tag: javaSpring

toc: true

toc_sticky: true

date: 2021-11-08

last_modified_at: 2021-11-08

---



# IoC, DI , 그리고 컨테이너



**제어의 역전 IoC(Inversion of Control)**

* 기존 프로그램은 클라이언트 구현 객체가 스스로 필요한 서버 구현 객체 생성, 연결, 실행했다. 한마디로 구현 객체가 프로그램의 제어 흐름을 스스로 조종했었다.

* 반면에 AppConfig가 등장한 이후에 구현 객체는 자신의 로직을 실행하는 역할만 담당! 프로그램의 제어 흐름은 이제 AppConfig가 가져감. 예를 들어, `OrderServiceImpl`은 필요한 인터페이스를 호출하지만 어떤 구현 객체가 실행될진 모른다.

* 프로그램에 대한 제어 흐름에 대한 권한은 모두 AppConfig가 가지고 있고, `OrderServiceImpl`도 AppConfig가 생성한다. `OrderServiceImpl`이 아닌 OrderService 인터페이스의 다른 구현 객체를 생성하고 실행할 수도 있다.

* 이렇듯 프로그램의 제어 흐름을 직접 제어하는 것이 아니라 외부에서 관리하는 것을 제어의 역전(IoC)라고 한다.

* AppConfig 코드

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
    
    public class AppConfig {
    
        public MemberService memberService() {
            return new MemberServiceImpl(memberRepository());
        }
    
        public OrderService orderService(){
            return new OrderServiceImpl(memberRepository(),discountPolicy());
        }
    
        private MemberRepository memberRepository() {
            return new MemoryMemberRepository();
        }
    
        public DiscountPolicy discountPolicy(){
            //return new FixDiscountPolicy();
            return new RateDiscountPolicy();
        }
    }
    ```





**프레임워크 vs 라이브러리**

* 프레임워크가 내가 작성한 코드를 제어하고, 대신 실행하면 그것은 프레임워크가 맞다(JUnit)
* 반면에 내가 작성한 코드가 직접 제어의 흐름을 담당한다면 그것은 프레임워크가 아니라 라이브러리다.



**의존관계 주입 DI(Dependency Injection)**

* `OrderServiceImpl` 은 `DiscountPolicy` 인터페이스에 의존한다. 실제 어떤 구현 객체가 사용될지는 모른다.
* 의존관계는 "정적인 클래스 의존 관계와, 실행 시점에 결정되는 동적인 객체(인스턴스) 의존 관계" 둘을 분리해서 생각해야 한다.
* **정적인 클래스 의존관계**
  * 클래스가 사용하는 import만 보고 의존관계를 쉽게 판단할 수 있다. 정적인 의존관계는 실행하지 않아도 분석 가능!
  * 그러나 이러한 클래스 의존관계만으로는 실제 어떤 객체가 `OrderServiceImpl`에 주입될지 알 수 없다.
  * <img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/springCore/springCore14.JPG?raw=true">
* **동적인 객체 인스턴스 의존 관계**
  * 애플리케이션 실행 시점에 실제 생성된 객체 인스턴스의 참조가 연결된 의존 관계다.
  * <img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/springCore/springCore15.JPG?raw=true">
  * 애플리케이션 **실행 시점(런타임)**에 외부에서 실제 구현 객체를 생성하고 클라이언트에 전달해서 클라이언트와 서버의 실제 의존관계가 연결 되는 것을 **의존관계 주입**이라고 한다!
  * 객체 인스턴스를 생성하고, 그 참조값을 전달해서 연결된다.
  * 의존관계 주입을 사용하면 클라이언트 코드를 변경하지 않고, 클라이언트가 호출하는 대상의 타입 인스턴스를 변경할 수 있다.





### IoC 컨테이너, DI 컨테이너

* AppConfig처럼 객체를 생성하고 관리하면서 의존관계를 연결해 주는 것을
* **IoC컨테이너** 또는 **DI 컨테이너** 라 한다.
* 의존관계 주입에 초점을 맞추어 최근에는 주로 DI 컨테이너라 한다.
* 또는 어샘블러, 오브젝트 팩토리 등으로 불리기도 한다.









### 참조

[김영한님의 인프런강의](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8)

