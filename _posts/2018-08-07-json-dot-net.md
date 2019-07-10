---
layout: single
title: C#에서 JSON 다루기(Json.NET)
date: 2018-08-07 09:45:30.000000000 +09:00
type: post
header:
    teaser: "https://user-images.githubusercontent.com/62210/29321053-746e3d0c-8196-11e7-99d2-94d6dc8afdfe.png"
    image: "https://user-images.githubusercontent.com/62210/29321053-746e3d0c-8196-11e7-99d2-94d6dc8afdfe.png"
categories:
- IT
tags: [csharp, json]
---

C#에서 [Json.NET]의 사용 예제를 정리해보았다. 왜냐면 내가 자꾸 잊어먹기 때문... 계속 업데이트 할 예정이다.

물론 [공식 문서](https://www.newtonsoft.com/json/help/html/Introduction.htm)가 최고다.

## GET parameter 받아오기

```csharp
string course_no = Request["course_no"];
string haksu_no = Request["haksu_no"];
```

## POST request 받아오기

```csharp
using System;
using System.Collections.Generic;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

string rawRequest = Request.Form[0];
JObject applyJObj = JObject.Parse(rawRequest);
```

## 예시용 Json

```json
// applyJObj	
{  
  "counselHopeTime": [
    {
      "key": 1,
      "date": "20180814",
      "time": "03"
    },
    {
      "key": 1533569780046,
      "date": "20180807",
      "time": "05"
    }
  ],
  "subject": {
    "life": [],
    "etc": ""
  },
  "problem": "문제다",
  "goal": "변화"
}
```

## Object 값 가져오기

```csharp
string goal = applyData["goal"].ToString();
```

## Object 안의 Object 값 가져오기

```csharp
string etc = applyData["subject"]["etc"].ToString();

```

## Array 안의 값을 ', '로 묶어 string으로 만들기

```csharp
string life = string.Join(", ", JsonConvert.DeserializeObject<List<string>>(applyData["subject"]["life"].ToString()).ToArray());
```

## property 순회하여 Array일 경우 처리

```csharp
foreach (JProperty property in applyJObj.Properties())
{
    if (property.Value.Type == JTokenType.Array) // Array일 경우
    {
        List<string> listInProperty = JsonConvert.DeserializeObject<List<string>> (property.Value.ToString());
        property.Value = string.Join(",", listInProperty.ToArray());
    }
}
```

## foreach로 element가 10개 있는 Array 만들기

```csharp
string[] hopeDateArr = {"0000000000", "0000000000", "0000000000", "0000000000", "0000000000", "0000000000", "0000000000", "0000000000", "0000000000", "0000000000"};
            
int hopeDateCnt = 0;
foreach(JObject hopeTime in applyData["counselHopeTime"])
{
    string dt = hopeTime["date"].ToString();
    hopeDateArr[hopeDateCnt] = dt;
    hopeDateCnt++;
}
```

## json 만들기

### Dictionary로 만들기

```csharp
string uploadedUrl = "http://whereTheFileWas.com/uploaded/what.jpg"
Dictionary<string, string> location = new Dictionary<string, string>
{
    {"location", uploadedUrl}
};
string json = JsonConvert.SerializeObject(location, Formatting.Indented);
```

## 불필요한 데이터 전송 줄이기

### 1개 컬럼만 있는 DataTable을 Array로 바꾸기

예시의 `DataTable`은 `APPLY_DATE` 컬럼 하나만 있는데, 이를 바로 `json`으로 바꾸면 불필요한 데이터 전송이 많다.
단순 `string`만 전송하기 위해 `ArrayList`로 바꿔 보내자.

```csharp
/* before
[
  {
    "APPLY_DATE": "20190311"
  },
  {
    "APPLY_DATE": "20190313"
  },
  {
    "APPLY_DATE": "20190314"
  }
]
*/
DataTable dt = new DataTable();
DataColumn column = new DataColumn();
column.DataType = System.Type.GetType("System.String");
column.ColumnName = "APPLY_DATE";
dt.Columns.Add(column);
DataRow row1 = dt.NewRow();
row1["APPLY_DATE"] = "20190311";
dt.Rows.Add(row1);
DataRow row2 = dt.NewRow();
row2["APPLY_DATE"] = "20190313";
dt.Rows.Add(row2);
DataRow row3 = dt.NewRow();
row3["APPLY_DATE"] = "20190314";
dt.Rows.Add(row3);

string json = JsonConvert.SerializeObject(dt, Formatting.Indented);
```

```csharp
/* after
[
  "20190311",
  "20190313",
  "20190314"
]
*/
ArrayList converted = new ArrayList(dt.Rows.Count);
foreach (DataRow row in dt.Rows)
{
    converted.Add(row.ItemArray[0].ToString());
}
string json = JsonConvert.SerializeObject(converted, Formatting.Indented);
```

[Json.NET]: https://www.newtonsoft.com/json
