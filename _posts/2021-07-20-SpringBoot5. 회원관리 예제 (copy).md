---

title: "5.SpringBoot 회원관리 예제"
excerpt: "비즈니스 요구사항 정리, 회원 도메인과 리포지토리 만들기"

categories: [devlog]

tag: javaSpring

toc: true

toc_sticky: true

date: 2021-07-20

last_modified_at: 2021-07-20
---





# 회원관리 예제



#### 비즈니스 요구사항 정리

* 데이터: 회원ID,이름
* 기능: 회원 등록,조회
* 아직 데이터 저장소가 선정되지 않음(가상의 시나리오)





<img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/users/users1.JPG?raw=true">

 

* 컨트롤러: 웹 MVC의 컨트롤러 역할
* 서비스: 핵심 비즈니스 로직 구현
* 리포지토리: 데이터베이스에 접근, 도메인 객체를 DB에 저장하고 관리
* 도메인[^1]: 비즈니스 도메인 객체. 예) 회원, 주문, 쿠폰 등등 주로 데이터베이스에 저장하고 관리됨





<img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/users/users2.JPG?raw=true">



* 아직 데이터 저장소가 선정되지 않아서, 우선 인터페이스로 구현 클래스를 변경할 수 있도록 설계
* 데이터 저장소는 RDB,NoSQL 등등 다양한 저장소를 고민중인 상황으로 가정
* 개발을 진행하기 위해서 초기 개발 단계에서는 구현체로 가벼운 메모리 기반의 데이터 저장소 사용



---

[^1]:도메인 객체란 내가 개발하고자 하는 영역을 분석하고, 그 분석의 결과로 도출된 객체를 말한다. 쇼핑몰을 만든다고 했을 때 쇼핑몰의 주 된 기능인 상품 구매에 사용되는 객체인 Member,Product,Purchase 등을 도메인 객체라고 한다.





#### 회원 도메인과 리포지토리 만들기





<img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/users/users3.JPG?raw=true">

```
package hello.hellospring.domain;

public class Member {

    private Long id;
    private String name;

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
}
```

* 회원 도메인 객체 생성
* id는 프로그램 내 분별하기위한 id, name은 이름



```
package hello.hellospring.repository;

import hello.hellospring.domain.Member;

import java.util.List;
import java.util.Optional;

public interface MemberRepository {
    Member save(Member member);
    Optional<Member> findById(Long id);
    Optional<Member> findByName(String name);
    List<Member> findAll();
}
```

* 리포지토리 인터페이스 생성
  * 4가지 기능 생성
    * save: 회원 저장
    * findById: id 찾기
    * findByName: name 찾기
    * findAll: 모든 회원 리스트 반환



---

Optional<T> 클래스는 Integer나 Double 클래스처럼 'T'타입의 객체를 포장해 주는 래퍼 클래스(Wrapper class)입니다. 따라서 Optional 인스턴스는 모든 타입의 참조 변수를 저장할 수 있습니다.

이러한 Optional 객체를 사용하면 예상치 못한 NullPointerException 예외를 제공되는 메소드로 간단히 회피할 수 있습니다.즉, 복잡한 조건문 없이도 널(null) 값으로 인해 발생하는 예외를 처리할 수 있게 됩니다.

null이 될 가능성 있음 -> Optional.ofNullable(내용)





```
package hello.hellospring.repository;

import hello.hellospring.domain.Member;

import java.util.*;

public class MemoryMemberRepository implements MemberRepository{

    private  static Map<Long, Member> store = new HashMap<>();
    private static long sequence = 0L;

    @Override
    public Member save(Member member) {
        member.setId(++sequence);
        store.put(member.getId(),member);
        return member;
    }

    @Override
    public Optional<Member> findById(Long id) {
        return Optional.ofNullable(store.get(id));
    }

    @Override
    public Optional<Member> findByName(String name) {
        store.values().stream().
                filter(member -> member.getName().equals(name))
                .findAny();
        return Optional.empty();
    }

    @Override
    public List<Member> findAll() {
        return new ArrayList<>(store.values());
    }
}
```

* 구현체(MemoryMemberRepository) 생성 (인터페이스 상속)
  * Map으로 id,name 매칭하여 저장
  * id를 저장할때마다 1씩 증가할 sequence 생성



* save기능 구현
  * 저장할때마다 sequence를 통해 id값 1씩 증가
  * store에 member.getId(),  Member객체가 저장  == id,(id,name)
* findById 기능 구현
  * null값이 나올수도 있으므로 ofNullable로 구현
* findByName
  * values() 메소드로 store안에 있는 객체들을 배열로 만들어서 리턴
  * stream[^1] 을통해 equals안에 있는 name과 같은 member만 반환
  * findAny[^2]을 통해 해당 요소 반환
  * return문을 통해 반환 (확인필요)
* findAll
  * store내에 저장되어있는 요소들을 배열리스트로 만들어서 반환



---

[^1]: 저장요소들을 람다식으로 처리할 수 있도록 해주는 반복자
[^2]:findFirst() vs findAny() : Stream을 직렬로 처리할 때 `findFirst()`와 `findAny()`는 동일한 요소를 리턴하며, 차이점이 없습니다.하지만 Stream을 병렬로 처리할 때는 차이가 있습니다.`findFirst()`는 여러 요소가 조건에 부합해도 Stream의 순서를 고려하여 가장 앞에 있는 요소를 리턴합니다.반면에 `findAny()`는 Multi thread에서 Stream을 처리할 때 가장 먼저 찾은 요소를 리턴합니다. 따라서 Stream의 뒤쪽에 있는 element가 리턴될 수 있습니다.





### 참조

[김영한님의 인프런강의](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%9E%85%EB%AC%B8-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8)
