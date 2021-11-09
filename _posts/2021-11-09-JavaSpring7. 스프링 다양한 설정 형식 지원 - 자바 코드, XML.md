---
title: "7. 스프링 다양한 설정 형식 지원"
excerpt: "JAVA, XML"

categories: [devlog]

tag: javaSpring

toc: true

toc_sticky: true

date: 2021-11-09

last_modified_at: 2021-11-09

---



# 다양한 설정 형식 지원



* 스프링 컨테이너는 다양한 형식의 설정 정보를 받아드릴 수 있게 유연하게 설계되어 있다.
  * 자바 코드 ,XML, Groovy 등등
  * <img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/springCore/springCore23.JPG?raw=true">



* 애노테이션 기반 자바 코드 설정 사용

  * `new AnnotationConfigApplicationContext(AppConfig.class)`
  * `AnnotationConfigApplicationContext` 클래스를 사용하면서 자바 코드로된 설정 정보를 넘기면 된다.

* XML 설정 사용

  * 스프링부트 사용 이후, XML 기반 설정은 잘 사용하지 않음.

  * 하지만 아직 많은 레거시 프로젝트 들이 XML로 되어있고, XML 사용 시 컴파일 없이 빈 설정 정보를 변경할 수 있는 장점도 있으므로 한번쯤 배워두는 것도 괜찮다.

  * `GenericXmlApplicationContext`를 사용하면서 `xml`설정 파일을 넘기면 된다

  * 코드

    * resources 아래에 appConfig.xml 파일 생성

      * ```
        <?xml version="1.0" encoding="UTF-8"?>
        <beans xmlns="http://www.springframework.org/schema/beans"
               xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
               xsi:schemaLocation="http://www.springframework.org/schema/beans http://
        www.springframework.org/schema/beans/spring-beans.xsd">
            <bean id="memberService" class="hello.core.member.MemberServiceImpl">
                <constructor-arg name="memberRepository" ref="memberRepository" />
            </bean>
            <bean id="memberRepository"
                  class="hello.core.member.MemoryMemberRepository" />
            <bean id="orderService" class="hello.core.order.OrderServiceImpl">
                <constructor-arg name="memberRepository" ref="memberRepository" />
                <constructor-arg name="discountPolicy" ref="discountPolicy" />
            </bean>
            <bean id="discountPolicy" class="hello.core.discount.RateDiscountPolicy" />
        </beans>
        ```

    * test 생성

      * ```
        package hello.core.xml;
        
        import hello.core.member.MemberService;
        import org.assertj.core.api.Assertions;
        import org.junit.jupiter.api.Test;
        import org.springframework.context.ApplicationContext;
        import org.springframework.context.support.GenericXmlApplicationContext;
        
        public class XmlAppContext {
        
            @Test
            void xmlAppContext() {
                ApplicationContext ac = new GenericXmlApplicationContext("appConfig.xml");
        
                MemberService memberService = ac.getBean("memberService", MemberService.class);
                Assertions.assertThat(memberService).isInstanceOf(MemberService.class);
            }
        }
        ```







### 참조

[김영한님의 인프런강의](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8)

