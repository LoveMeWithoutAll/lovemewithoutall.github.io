---
layout: single
title: lodash usage examples
date: 2020-05-28 12:00:30.000000000 +09:00
type: post
header:
    teaser: "https://lodash.com/assets/img/lodash.svg"
    image: "https://lodash.com/assets/img/lodash.svg"
categories:
- IT
tags: [javascript, lodash]
---

I put together some [Lodash] usage examples. Because I keep forgetting them... I plan to keep updating this.

## array multi group by

Build a GROUP BY based on two columns, `BUILDING` and `BUILDING_NAME`. These two columns have a 1:1 relationship as `CODE` and `VALUE`.

```json
// Before
[
  {BUILDING: "01", BUILDING_NAME: "Building 1", ROOM_NO: "1-001B", ROOM: "1-001B"}
  ,{BUILDING: "01", BUILDING_NAME: "Building 1", ROOM_NO: "1-002", ROOM: "1-002"}
  ,{BUILDING: "01", BUILDING_NAME: "Building 1", ROOM_NO: "1-011A", ROOM: "1-011A"}
  ,{BUILDING: "01", BUILDING_NAME: "Building 1", ROOM_NO: "1-011B", ROOM: "1-011B"}  
  // ...
]

// GROUP BY...

// After
[
  BUILDING: "01"
  BUILDING_NAME: "Building 1"
  ROOM: [
    {ROOM_NO: "1-001B", ROOM: "1-001B"}
    ,{ROOM_NO: "1-002", ROOM: "1-002"}
    ,{ROOM_NO: "1-011A", ROOM: "1-011A"}
    ,{ROOM_NO: "1-011B", ROOM: "1-011B"}    
    // ...
  ]
]
```

The key is using `groupBy` and `map`, and using `groupBy` with `chain`.

```javascript
json = _.chain(json)
        .groupBy("BUILDING_NAME") // group by BUILDING_NAME
        .map(function(v, i) { // groupBy returns an object, so use map to turn it back into an array
          return {
            BUILDING: _.get(_.find(v, 'BUILDING'), 'BUILDING'), // for BUILDING, pick just one with _.find
            BUILDING_NAME: i, // BUILDING_NAME is the grouping key, so it's the index
            ROOM: _.chain(v) // ROOM and ROOM_NO also have a 1:1 relationship, so just groupBy again
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

## Iterating over Objects in an Array and building a unique Array from the value of a specific Property

1. Iterate over every Object in the Array, pull out the value of the `ROOM_NO` property, and build an Array, then
1. make the values in the Array unique.

```json
// Before
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

/// After
["5E405", "5S535", "6-101"]
```

The key is using the `map` function to go through every Object and pull out only the value of a specific Property, then using the `uniq` function.

```javascript
let result = _.chain(this.selected)
              .map((o) => o.ROOM_NO)
              .uniq()
              .value()
```

## Iterating over Objects in an Array and adding Properties

1. Iterate over every Object in the Array and add 2 Properties.
1. The Properties to add are
  - HOPE1: `HOPE1_DATE + HOPE1_TIME'
  - HOPE2: `HOPE2_DATE + HOPE2_TIME'

```javascript
// Before
[{
  "HOPE1_DATE": "Monday",
  "HOPE1_TIME": "1pm-2pm",
  "HOPE2_DATE": "Monday",
  "HOPE2_TIME": "2pm-3pm"
}, {
  "HOPE1_DATE": "Monday",
  "HOPE1_TIME": "1pm-2pm",
  "HOPE2_DATE": "Monday",
  "HOPE2_TIME": "2pm-3pm"
}]

// After
[{
  "HOPE1_DATE": "Monday",
  "HOPE1_TIME": "1pm-2pm",
  "HOPE2_DATE": "Monday",
  "HOPE2_TIME": "2pm-3pm",
  "HOPE1": "Monday 1pm-2pm",
  "HOPE2": "Monday 2pm-3pm"
}, {
  "HOPE1_DATE": "Monday",
  "HOPE1_TIME": "1pm-2pm",
  "HOPE2_DATE": "Monday",
  "HOPE2_TIME": "2pm-3pm",
  "HOPE1": "Monday 1pm-2pm",
  "HOPE2": "Monday 2pm-3pm"
}]
```

The key is `assignIn`.

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
