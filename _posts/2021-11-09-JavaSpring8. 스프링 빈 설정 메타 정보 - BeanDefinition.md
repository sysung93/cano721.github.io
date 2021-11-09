---
title: "8. 스프링 빈 설정 메타 정보 - BeanDefinition"
excerpt: ""

categories: [devlog]

tag: javaSpring

toc: true

toc_sticky: true

date: 2021-11-09

last_modified_at: 2021-11-09

---



# 스프링 빈 설정 메타 정보 - BeanDefinition

* 스프링은 어떻게 다양한 설정 형식을 지원하는가? 그 중심에는 `BeanDefinition`이라는 추상화가 있다.
* 쉽게 이야기해서 "역할과 구현을 개념적으로 나눈 것"
  * XML 또는 자바코드를 읽어서 BeanDefinition을 만들면 된다.
  * 스프링 컨테이너는 자바코드인지,XML인지 몰라도 된다. 오직 BeanDefinition만 알면 된다.
* `BeanDefinition`을 빈 설정 메타정보라고 한다.
  * `@Bean`, `<Bean>` 당 각각 하나씩 메타 정보가 생성된다.
* 스프링 컨테이너는 이 메타정보를 기반으로 스프링 빈을 생성한다.



<img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/springCore/springCore24.JPG?raw=true">

<img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/springCore/springCore25.JPG?raw=true">

* `AnnotationConfigApplicationContext`는 `AnnotatedBeanDefinitionReader`를 사용해서 `AppConfig.class`를 읽고 `BeanDefinition`을 생성한다.
* xml의 경우 `GenericXmlApplicationContext`는 `XmlBeanDefinitionReader`를 사용해서 `appConfig.xml` 설정 정보를 읽고 `BeanDefinition`을 생성한다.
* 새로운 형식의 설정 정보가 추가되면, XxxBeanDefinitionReader를 만들어서 `BeanDefinition`을 생성하면 된다.



**BeanDefinition**

<img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/springCore/springCore26.JPG?raw=true">

* 확인코드

  * ```
    package hello.core.beandefinition;
    
    import hello.core.AppConfig;
    import org.junit.jupiter.api.DisplayName;
    import org.junit.jupiter.api.Test;
    import org.springframework.beans.factory.config.BeanDefinition;
    import org.springframework.context.annotation.AnnotationConfigApplicationContext;
    
    public class BeanDefinitionTest {
    
        AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
    
        @Test
        @DisplayName("빈 설정 메타정보 확인")
        void findApplicationBean(){
            String[] beanDefinitionNames = ac.getBeanDefinitionNames();
            for (String beanDefinitionName : beanDefinitionNames) {
                BeanDefinition beanDefinition = ac.getBeanDefinition(beanDefinitionName);
    
                if(beanDefinition.getRole() == BeanDefinition.ROLE_APPLICATION){
                    System.out.println("beanDefinitionName = " + beanDefinitionName + " beanDefinition = " + beanDefinition);
                }
            }
        }
    }
    ```



실무에선 직접 사용할일이 거의 없음.



### 참조

[김영한님의 인프런강의](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8)

