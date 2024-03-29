---
layout: single
title: lodash 사용 예제
date: 2020-05-28 12:00:30.000000000 +09:00
type: post
header:
    teaser: "https://lodash.com/assets/img/lodash.svg"
    image: "https://lodash.com/assets/img/lodash.svg"
categories:
- IT
tags: [javascript, lodash]
---

[Lodash] 사용 예제를 정리해보았다. 왜냐면 내가 자꾸 잊어먹기 때문... 계속 업데이트 할 예정이다.

## array multi group by

`BUILDING`과 `BUILDING_NAME`, 2개 컬럼을 기준으로 GROUP BY를 만든다. 이 2개 컬럼은 `CODE`, `VALUE`로서 1:1 관계다.

```json
// 변경 전
[
  {BUILDING: "01", BUILDING_NAME: "1호관", ROOM_NO: "1-001B", ROOM: "1-001B"}
  ,{BUILDING: "01", BUILDING_NAME: "1호관", ROOM_NO: "1-002", ROOM: "1-002"}
  ,{BUILDING: "01", BUILDING_NAME: "1호관", ROOM_NO: "1-011A", ROOM: "1-011A"}
  ,{BUILDING: "01", BUILDING_NAME: "1호관", ROOM_NO: "1-011B", ROOM: "1-011B"}  
  // ...
]

// GROUP BY...

// 변경 후
[
  BUILDING: "01"
  BUILDING_NAME: "1호관"
  ROOM: [
    {ROOM_NO: "1-001B", ROOM: "1-001B"}
    ,{ROOM_NO: "1-002", ROOM: "1-002"}
    ,{ROOM_NO: "1-011A", ROOM: "1-011A"}
    ,{ROOM_NO: "1-011B", ROOM: "1-011B"}    
    // ...
  ]
]
```

핵심은 `groupBy`와 `map`, 그리고 `groupBy`를 `chain`으로 쓰는 것이다.

```javascript
json = _.chain(json)
        .groupBy("BUILDING_NAME") // BUILDING_NAME으로 group by를 하고
        .map(function(v, i) { // groupBy를 하면 object가 return되니, map을 써서 array로 다시 바꿔주자
          return {
            BUILDING: _.get(_.find(v, 'BUILDING'), 'BUILDING'), // BUILDING은 _.find로 하나만 골라내고
            BUILDING_NAME: i, // BUILDING_NAME이 기준자라 index다
            ROOM: _.chain(v) // ROOM과 ROOM_NO도 1:1 관계니까 groupBy를 또 해주면 된다
                  .groupBy("ROOM_NO")
                  .map((v, i) => {
                    return {
                      ROOM_NO: i,
                      ROOM: _.get(_.find(v, 'ROOM'), 'ROOM')
                    }
                  }).value()
          }
        })
        .orderBy(['BUILDING'])
        .value();
```

## Array 안의 Object를 순회하며 특정 Property의 값을 unique 한 Array로 만들기

1. Array 안의 모든 Object를 순회하며 `ROOM_NO` property의 value를 뽑아 Array로 만든 후
1. Array 안의 값은 unique하게 재편한다

```json
// 변경 전
[{
  "id": 0,
  "ROOM_NO": "5E405"
}, {
  "id": 3,
  "ROOM_NO": "5S535"
}, {
  "id": 4,
  "ROOM_NO": "5S535"
}, {
  "id": 6,
  "ROOM_NO": "6-101"
}]

/// 변경 후
["5E405", "5S535", "6-101"]
```

핵심은 `map` 함수로 모든 Object를 조회하며 특정 Propert의 값만 뽑고, `uniq` 함수를 쓰는 것이다.

```javascript
let result = _.chain(this.selected)
              .map((o) => o.ROOM_NO)
              .uniq()
              .value()
```

## Array 안의 Object를 순회하며 Property 추가하기

1. Array 안의 모든 Object를 순회하며 Property 2개를 추가한다.
1. 추가할 Property는
  - HOPE1: `HOPE1_DATE + HOPE1_TIME'
  - HOPE2: `HOPE2_DATE + HOPE2_TIME'

```javascript
// 변경전
[{
  "HOPE1_DATE": "월요일",
  "HOPE1_TIME": "13시-14시",
  "HOPE2_DATE": "월요일",
  "HOPE2_TIME": "14시-15시"
}, {
  "HOPE1_DATE": "월요일",
  "HOPE1_TIME": "13시-14시",
  "HOPE2_DATE": "월요일",
  "HOPE2_TIME": "14시-15시"
}]

// 변경 후
[{
  "HOPE1_DATE": "월요일",
  "HOPE1_TIME": "13시-14시",
  "HOPE2_DATE": "월요일",
  "HOPE2_TIME": "14시-15시",
  "HOPE1": "월요일 13시-14시",
  "HOPE2": "월요일 14시-15시"
}, {
  "HOPE1_DATE": "월요일",
  "HOPE1_TIME": "13시-14시",
  "HOPE2_DATE": "월요일",
  "HOPE2_TIME": "14시-15시",
  "HOPE1": "월요일 13시-14시",
  "HOPE2": "월요일 14시-15시"
}]
```

핵심은 `assignIn`이다.

```javascript
_.map(list, o => {
    return _.assignIn(
      o,
      { HOPE1: `${o.HOPE1_DATE} ${o.HOPE1_TIME}` },
      { HOPE2: `${o.HOPE2_DATE} ${o.HOPE2_TIME}` }
    )
  })
```

[Lodash]: https://lodash.com/
