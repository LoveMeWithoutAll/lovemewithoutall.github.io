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

While using Spring Data JPA, I went through the pain of sorting not working. As usual, I tried to apply `order by` through a Spring Data method name, but for some reason it couldn't find the column name, so an error occurred where the query statement couldn't be generated. So I found a simple solution using the `Sort` object.

First, find the column name in the Entity below. Here, it's `vDate`. Note that this is not the column name of the DB table.

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

Add a `sort parameter` to the Repository method.

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

In the Service, create a `Sort` object and pass it in. Doing it like the code below appends `order by V_DATE DESC` to the query statement.

```java
// Service
public List<VisitorHistory> getVisitorsByTypeAndId(String id, String type) {
    Sort sort = sortByvDate();
    final List<VisitorHistory> visitors = visitorHistoryRepository.findAllByvUseridAndType(id, typeCode, sort);
    return visitors;
}

private Sort sortByvDate() {
    return Sort.by(Sort.Direction.DESC, "vDate");
}
```

Now it works just fine. Done!

![Spring army](https://spring.io/img/iconsprite-3f43d82fa4826e2d08fc096eb424c09e.png)

Out of this huge Spring army... I wonder what percentage of it I actually understand while using it?
