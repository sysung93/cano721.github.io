---
title: "5. 스프링 컨테이너, 스프링 빈 조회"
excerpt: "스프링 컨테이너, 빈 생성, 조회"

categories: [devlog]

tag: javaSpring

toc: true

toc_sticky: true

date: 2021-11-08

last_modified_at: 2021-11-09

---



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





### 스프링 컨테이너

* `ApplicationContext`를 스프링 컨테이너라 한다.

* 기존에는 개발자가 `AppConfig`를 사용해서 직접 객체를 생성하고 DI를 했지만, 이제부터는 스프링 컨테이너를 통해서 사용한다.

* `Configuration`이 붙은 `AppConfig`를 설정정보로 사용하고, `@Bean`이라 적힌 메서드를 모두 호출해서 반환된 객체를 스프링 컨테이너에 등록한다. 이렇게 스프링 컨테이너에 등록된 객체를 스프링 빈이라고 한다.

* 스프링 빈은 `@Bean` 어노테이션이 붙은 메서드의 명을 스프링 빈의 이름으로 사용한다.

* 이전에는 필요한 객체를 AppConfig를 사용해서 직접 조회했지만, 이제는 스프링 컨테이너를 통해서 필요한 스프링 빈을 찾아야한다. 스프링 빈은 `applicationContext.getBean()`메서드를 사용해서 찾을 수 있다.

* 코드

  * ```
    ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
    ```

* `ApplicationContext`는 인터페이스다.

* 스프링 컨테이너는 XML을 기반으로 만들 수 있고, 에노테이션 기반의 자바 설정 클래스로 만들 수 있다.

* 직전에 `AppConfig`를 사용했던 방식이 애노테이션 기반의 자바 설정 클래스로 스프링 컨테이너를 만든 것이다.

  * `new AnnotaionConfigApplicationContext(AppConfig.class)`
  * 이 클래스는 `ApplicationContext`의 구현체이다.



**스프링 컨테이너 생성 과정**

* <img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/springCore/springCore16.JPG?raw=true">
  * `new AnnotaionConfigApplicationContext(AppConfig.class)`
  * 스프링 컨테이너를 생성할 때는 구성 정보를 지정해주어야 함.
  * 여기서 `AppConfig.class`를 구성 정보로 지정.
* <img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/springCore/springCore17.JPG?raw=true">
  * 스프링 컨테이너는 파라미터로 넘어온 설정 클래스 정보를 사용해서 스프링 빈을 등록한다.
  * 메서드 명을 키값, 리턴값을 밸류값으로 저장.
* **빈 이름**
  * 빈 이름은 디폴트값으로 메서드 이름을 사용함.
  * 빈 이름을 직접 부여할 수도 있음. `@Bean(name="memberService2")`

| **주의: 빈 이름은 항상 다른 이름을 부여** 해야함.  같은 이름을 부여하면, 다른 빈이 무시되거나, 기존 빈을 덮어버리거나 설정에 따라 오류가 발생.



* **스프링 빈 의존관계 설정 - 준비**
  * <img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/springCore/springCore18.JPG?raw=true">
* **스프링 빈 의존관계 설정 - 완료**
  * <img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/springCore/springCore19.JPG?raw=true">
  * 스프링 컨테이너는 설정 정보를 참고해서 의존관계를 주입(DI)한다.
  * 단순히 자바 코드를 호출하는 것 같지만, 차이가 있다. 이 차이는 뒤에 싱글톤 컨테이너에서 설명한다.



| **참고**

스프링 빈을 생성하고 ,의존관꼐를 주입하는 단계가 나누어져 있다. 그런데 이렇게 자바 코드로 스프링 빈을 등록하면 생성자를 호출하면서 의존관계 주입도 한번에 처리된다. 여기서는 이해를 돕기 위해 개념적으로 나누어 설명했다. 



### 컨테이너에 등록된 모든 빈 조회



* Test -> beanfind 패키지 생성 -> ApplicationContextInfoTest 클래스 생성

  * 코드

  * ```
    package hello.core.beanfind;
    
    import hello.core.AppConfig;
    import org.junit.jupiter.api.DisplayName;
    import org.junit.jupiter.api.Test;
    import org.springframework.beans.factory.config.BeanDefinition;
    import org.springframework.context.annotation.AnnotationConfigApplicationContext;
    
    // junit5 부터 퍼블릭 설정 안해도 됨
    
    class ApplicationContextInfoTest {
    
        AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
    
        @Test
        @DisplayName("모든 빈 출력하기")
        void findAllBean(){
            String[] beanDefitionNames = ac.getBeanDefinitionNames();
            for (String beanDefitionName : beanDefitionNames) {
                Object bean = ac.getBean(beanDefitionName);
                System.out.println("name = " + beanDefitionName + " object = " + bean);
            }
        }
    
        @Test
        @DisplayName("애플리케이션 빈 출력하기")
        void findApplicationBean(){
            String[] beanDefitionNames = ac.getBeanDefinitionNames();
            for (String beanDefitionName : beanDefitionNames) {
                BeanDefinition beanDefinition = ac.getBeanDefinition(beanDefitionName);
    
                // Role ROLE_APPLICATION: 직접 등록한 애플리케이션 빈
                // Role ROLE_INFRASTRUCTURE: 스프링이 내부에서 사용하는 빈
                if (beanDefinition.getRole() == BeanDefinition.ROLE_APPLICATION){
                    Object bean = ac.getBean(beanDefitionName);
                    System.out.println("name = " + beanDefitionName + " object = " + bean);
                }
            }
        }
    }
    ```

* 모든 빈 출력하기

  * 해당 코드 실행하면 스프링에 등록된 모든 빈 출력함.
  * `ac.getBeanDefinitionNames()`: 스프링에 등록된 모든 빈 이름을 조회
  * `ac.getBean()`: 빈 이름으로 빈 객체(인스턴스)를 조회

* 애플리케이션 빈 출력하기

  * 스프링이 내부에서 사용하는 빈은 `getRole()`로 구분 가능
    * `Role ROLE_APPLICATION` : 사용자가 정의한 빈
    * `Role ROLE_INFRASTRUCTURE`: 스프링이 내부에서 사용하는 빈



### 스프링 빈 조회 - 기본



* 코드

  * ```
    package hello.core.beanfind;
    
    
    import hello.core.AppConfig;
    import hello.core.member.MemberService;
    import hello.core.member.MemberServiceImpl;
    import org.assertj.core.api.Assertions;
    import org.junit.jupiter.api.DisplayName;
    import org.junit.jupiter.api.Test;
    import org.springframework.beans.factory.NoSuchBeanDefinitionException;
    import org.springframework.beans.factory.config.BeanDefinition;
    import org.springframework.context.annotation.AnnotationConfigApplicationContext;
    
    import static org.assertj.core.api.Assertions.*;
    import static org.junit.jupiter.api.Assertions.assertThrows;
    
    public class ApllcationContextBasicFindTest {
    
        AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
    
        @Test
        @DisplayName("빈 이름으로 조회")
        void findBeanByName(){
            MemberService memberService = ac.getBean("memberService", MemberService.class);
            assertThat(memberService).isInstanceOf(MemberServiceImpl.class);
        }
    
        @Test
        @DisplayName("이름 없이 타입으로만 조회")
        void findBeanByType(){
            MemberService memberService = ac.getBean(MemberService.class);
            assertThat(memberService).isInstanceOf(MemberServiceImpl.class);
        }
    
        // 구체타입으로 조회시엔 유연성이 떨어짐; 추상화에 의존하자!(인터페이스)
        @Test
        @DisplayName("구체 타입으로 조회")
        void findBeanByName2(){
            MemberService memberService = ac.getBean("memberService", MemberServiceImpl.class);
            assertThat(memberService).isInstanceOf(MemberServiceImpl.class);
        }
    
        @Test
        @DisplayName("빈 이름으로 조회X")
        void findBeanByNameX(){
            // ac.getBean("xxxxx", MemberService.class);
            assertThrows(NoSuchBeanDefinitionException.class,
                    () -> ac.getBean("xxxxx", MemberService.class));
        }
    }
    ```

* ac.getBean(빈이름,타입) or ac.getBean(타입) 으로 조회 가능

* 조회대상 스프링 빈이 없으면 예외 발생

  * NoSuchBeanDefinitionException

  * junit에 있는 assertThrows를 이용하여 예외 테스트 가능



### 스프링 빈 조회 - 동일한 타입이 둘 이상

* 타입으로 조회 시 같은 타입의 스프링 빈이 둘 이상이면 오류 발생. 이때는 빈 이름도 같이 지정

* ac.getBeansOfType() 을 사용하면 해당 타입의 모든 빈을 조회 가능

* 코드

  * ```
    package hello.core.beanfind;
    
    import hello.core.member.MemberRepository;
    import hello.core.member.MemoryMemberRepository;
    import org.junit.jupiter.api.Assertions;
    import org.junit.jupiter.api.DisplayName;
    import org.junit.jupiter.api.Test;
    import org.springframework.beans.factory.NoUniqueBeanDefinitionException;
    import org.springframework.context.annotation.AnnotationConfigApplicationContext;
    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;
    
    import java.util.Map;
    
    import static org.assertj.core.api.Assertions.assertThat;
    
    public class ApplicationContextSameBeanFindTest {
    
        AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(SameBeanConfig.class);
    
        @Test
        @DisplayName("타입으로 조회시 같은 타입이 둘 이상 있으면, 중복 오류가 발생한다")
        void findBeanByTypeDuplicate(){
            Assertions.assertThrows(NoUniqueBeanDefinitionException.class,
                    () -> ac.getBean(MemberRepository.class));
        }
    
        @Test
        @DisplayName("타입으로 조회시 같은 타입이 둘 이상 있으면, 빈 이름을 지정하면 된다")
        void findBeanByName(){
            MemberRepository memberRepository = ac.getBean("memberRepository1",MemberRepository.class);
            assertThat(memberRepository).isInstanceOf(MemberRepository.class);
        }
    
        @Test
        @DisplayName("특정 타입을 모두 조회하기")
        void findAllBeanByType(){
            Map<String, MemberRepository> beansOfType = ac.getBeansOfType(MemberRepository.class);
            for (String key : beansOfType.keySet()) {
                System.out.println("key = " + key + " value = " + beansOfType.get(key));
            }
            System.out.println("beansOfType = " + beansOfType);
            assertThat(beansOfType.size()).isEqualTo(2);
        }
    
    
        // 이 테스트내에서만 쓰일 설정정보
        @Configuration
        static class SameBeanConfig {
            @Bean
            public MemberRepository memberRepository1(){
                return new MemoryMemberRepository();
            }
            @Bean
            public  MemberRepository memberRepository2(){
                return new MemoryMemberRepository();
            }
        }
    }
    ```



### 스프링 빈 조회 - 상속관계

* 부모 타입으로 조회하면, 자식 타입도 함께 조회한다.

* 그래서 모든 자바 객체의 최고 부모인 `Object`타입으로 조회하면, 모든 스프링 빈을 조회한다.

* <img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/springCore/springCore19.JPG?raw=true">

* 코드

  * ```
    package hello.core.beanfind;
    
    import hello.core.discount.DiscountPolicy;
    import hello.core.discount.FixDiscountPolicy;
    import hello.core.discount.RateDiscountPolicy;
    import org.junit.jupiter.api.DisplayName;
    import org.junit.jupiter.api.Test;
    import org.springframework.beans.factory.NoUniqueBeanDefinitionException;
    import org.springframework.context.annotation.AnnotationConfigApplicationContext;
    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;
    
    import java.util.Map;
    
    import static org.assertj.core.api.Assertions.assertThat;
    import static org.junit.jupiter.api.Assertions.assertThrows;
    
    public class ApplicationContextExtendsFindTest {
    
        AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(TestConfig.class);
    
        @Test
        @DisplayName("부모 타입으로 조회시, 자식이 둘 이상 있으면, 중복 오류가 발생한다")
        void findBeanByParentTypeDuplicate(){
            assertThrows(NoUniqueBeanDefinitionException.class,
                    () -> ac.getBean(DiscountPolicy.class));
        }
    
        @Test
        @DisplayName("부모 타입으로 조회시, 자식이 둘 이상 있으면, 빈 이름을 지정하면 된다")
        void findBeanByParentTypeBeanName(){
            DiscountPolicy rateDiscountPolicy = ac.getBean("rateDiscountPolicy", DiscountPolicy.class);
            assertThat(rateDiscountPolicy).isInstanceOf(RateDiscountPolicy.class);
        }
    
        @Test
        @DisplayName("특정 하위 타입으로 조회")
        void findBeanBySubType(){
            RateDiscountPolicy bean = ac.getBean(RateDiscountPolicy.class);
            assertThat(bean).isInstanceOf(RateDiscountPolicy.class);
        }
    
        @Test
        @DisplayName("부모 타입으로 모두 조회하기")
        void findAllBeanByParentType(){
            Map<String, DiscountPolicy> beansOfType = ac.getBeansOfType(DiscountPolicy.class);
            assertThat(beansOfType.size()).isEqualTo(2);
            for (String key : beansOfType.keySet()) {
                System.out.println("key = " + key + " value = " + beansOfType.get(key));
            }
        }
    
        @Test
        @DisplayName("부모 타입으로 모두 조회하기 - Object")
        void findAllBeanObjectType(){
            Map<String, Object> beansOfType = ac.getBeansOfType(Object.class);
            for (String key : beansOfType.keySet()) {
                System.out.println("key = " + key + " value = " + beansOfType.get(key));
            }
        }
    
        @Configuration
        static class TestConfig{
    
            @Bean
            public DiscountPolicy rateDiscountPolicy(){
                return new RateDiscountPolicy();
            }
    
            @Bean
            public DiscountPolicy fixDiscountPolicy(){
                return new FixDiscountPolicy();
            }
        }
    }
    ```





### 참조

[김영한님의 인프런강의](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8)

