---
layout: single
title: Excel upload with Sheet.js(feat. Vue.js)
date: 2020-06-22 08:45:30.000000000 +09:00
type: post
header:
    teaser: "https://sheetjs.com/sketch128.png"
    image: "https://sheetjs.com/sketch128.png"
categories:
- IT
tags: [Vue.js, JSON, excel, javascript]
---

Working code link: [CodePen](https://codepen.io/lovemewithoutall/pen/JjGWPdG)

[Exporting JSON to Excel is possible](https://lovemewithoutall.github.io/it/json-to-excel/). Of course, Uploading an Excel file & converting to JSON are possible by using [SheetJS].

First of all, let's install [SheetJS].

```bash
npm install --save xlsx
```

The example code below shows how to Upload Excel file. I used [Vue.js] single component in below example. But it does not matter if you understand javascript. `accept` attribute restrict file format for uploading. Only excel format is accepted.

After You uploaded an excel file, excel data would be showed on below.

```html
<!-- Below code is tested on SheetJS v0.16.2 -->
<template>
  <div id="app">
    <h1>excel import with vue & sheet.js!</h1>
    <div>
        <input 
          type="file" 
          @change="excelExport"
          accept="application/vnd.openxmlformats-officedocument.spreadsheetml.sheet"/>
    </div>
    <div>
      {{ this.excelData }}
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      excelData: []
    };
  },
  methods: {
    excelExport(event) {
      var input = event.target;
      var reader = new FileReader();
      reader.onload = () => {
        var fileData = reader.result;
        var wb = XLSX.read(fileData, {type : 'binary'});
        wb.SheetNames.forEach((sheetName) => {
	        var rowObj =XLSX.utils.sheet_to_json(wb.Sheets[sheetName]);	        
          this.excelData = JSON.stringify(rowObj)
        })
      };
      reader.readAsBinaryString(input.files[0]);
    }
  }
};
</script>

<!-- The CSS below is just CSS. No necessary. -->
<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
```

End!

## Reference

Document for XLSX.utils: https://github.com/SheetJS/js-xlsx#utility-functions

[SheetJS]: https://sheetjs.com/
[Vue.js]: https://vuejs.org/