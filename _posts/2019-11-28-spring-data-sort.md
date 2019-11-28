---
layout: single
title: Spring Data JPA Sort(Order by)
date: 2019-11-28 22:33:30.000000000 +09:00
type: post
header:
    teaser: "https://spring.io/img/iconsprite-3f43d82fa4826e2d08fc096eb424c09e.png"
    image: "https://spring.io/img/iconsprite-3f43d82fa4826e2d08fc096eb424c09e.png"
categories:
- IT
tags: [Java, JPA, Spring]
---

Spring Data JPA를 쓰다가 Sorting이 안되는 고통을 겪었다. 평소처럼 Spring Data method명으로 `order by`를 먹이려는데, 왜인지는 모르겠으나 컬럼명을 발견하지 못하여, 쿼리문이 만들어지지 못하는 오류가 발생했다. 그래서 `Sort` 객체를 사용한 간단한 해결책을 찾았다.

우선 아래의 Entity에서 컬럼명을 찾는다. 여기서는 `vDate`다. DB 테이블의 컬럼명이 아니니 주의할 것.

```java
// Entity

// package...
// import...

@Entity
// Annotations...
public class VisitorHistory {
    // Columns...
    @Temporal(TemporalType.TIMESTAMP)
    @Column(name = "V_DATE", nullable = false, insertable = false)
    private Date vDate;
}
```

Repository의 메소드에 `sort 파라미터`를 추가한다.

```java
// Repository

// package ...
// import ...
import org.springframework.data.domain.Sort;

import java.util.Date;

@Repository
public interface VisitorHistoryRepository extends JpaRepository<VisitorHistory, Integer> {
    List<VisitorHistory> findAllByvDateBetween(Date start, Date end, Sort sort);
}
```

Service에서 `Sort` 객체를 만들어 넣어준다. 아래 코드처럼 하면 쿼리문에 `order by V_DATE DESC` 가 붙는다.

```java
// Service
public List<VisitorHistory> getVisitorsByTypeAndId(String id, String type) {
    Sort sort = sortByvDate();
    final List<VisitorHistory> visitors = visitorHistoryRepository.findAllByvUseridAndType(id, typeCode, sort);
    return visitors;
}

private Sort sortByvDate() {
    return new Sort(Sort.Direction.DESC, "vDate");
}
```

이제 잘 돌아간다. 끝!

![Spring army](https://spring.io/img/iconsprite-3f43d82fa4826e2d08fc096eb424c09e.png)

나는 이 많은 스프링 대군세... 중에서 몇 퍼센트나 알고 쓰고 있는걸까?
