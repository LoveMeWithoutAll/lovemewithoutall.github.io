---
layout: single
title: Working with JSON in C# (Json.NET)
date: 2018-08-07 09:45:30.000000000 +09:00
type: post
header:
    teaser: "https://user-images.githubusercontent.com/62210/29321053-746e3d0c-8196-11e7-99d2-94d6dc8afdfe.png"
    image: "https://user-images.githubusercontent.com/62210/29321053-746e3d0c-8196-11e7-99d2-94d6dc8afdfe.png"
categories:
- IT
tags: [csharp, json]
---

I've put together some usage examples for [Json.NET] in C#. The reason being that I keep forgetting them... I plan to keep updating this.

Of course, the [official docs](https://www.newtonsoft.com/json/help/html/Introduction.htm) are the best resource.

## Getting GET parameters

```csharp
string course_no = Request["course_no"];
string haksu_no = Request["haksu_no"];
```

## Getting a POST request

```csharp
using System;
using System.Collections.Generic;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

string rawRequest = Request.Form[0];
JObject applyJObj = JObject.Parse(rawRequest);
```

## Example JSON

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
  "problem": "this is the problem",
  "goal": "change"
}
```

## Getting an Object value

```csharp
string goal = applyData["goal"].ToString();
```

## Getting an Object value inside an Object

```csharp
string etc = applyData["subject"]["etc"].ToString();

```

## Joining the values inside an Array with ', ' into a string

```csharp
string life = string.Join(", ", JsonConvert.DeserializeObject<List<string>>(applyData["subject"]["life"].ToString()).ToArray());
```

## Iterating over properties and handling the Array case

```csharp
foreach (JProperty property in applyJObj.Properties())
{
    if (property.Value.Type == JTokenType.Array) // when it's an Array
    {
        List<string> listInProperty = JsonConvert.DeserializeObject<List<string>> (property.Value.ToString());
        property.Value = string.Join(",", listInProperty.ToArray());
    }
}
```

## Building an Array of 10 elements with foreach

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

## Building JSON

### Building it with a Dictionary

```csharp
string uploadedUrl = "http://whereTheFileWas.com/uploaded/what.jpg"
Dictionary<string, string> location = new Dictionary<string, string>
{
    {"location", uploadedUrl}
};
string json = JsonConvert.SerializeObject(location, Formatting.Indented);
```

## Reducing unnecessary data transfer

### Converting a single-column DataTable into an Array

The example `DataTable` has only one column, `APPLY_DATE`. Converting it directly to `json` results in a lot of unnecessary data transfer.
To send only the plain `string` values, let's convert it to an `ArrayList` before sending.

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
