---
layout: single
title: JSON to Excel with Sheet.js(feat. Vue.js)
date: 2018-07-11 09:45:30.000000000 +09:00
type: post
header:
    teaser: "https://sheetjs.com/sketch128.png"
    image: "https://sheetjs.com/sketch128.png"
categories:
- IT
tags: [Vue.js, JSON, excel, javascript]
---

For Exporting JSON to Excel file, [SheetJS] is useful. [SheetJS] is complicated to use, but powerful and well maintained. I tried to use [vue-json-excel](https://github.com/jecovier/vue-json-excel), but on v0.2.5, the package throw an [error](https://github.com/jecovier/vue-json-excel/issues/48) and has a [problem](https://github.com/jecovier/vue-json-excel/issues/2).

First of all, let's install [SheetJS].

```bash
npm install xlsx
```

The example code below shows how to download Excel file from JSON. I used [Vue.js] single component in below example. But it does not matter if you understand javascript.

```html
<!-- Below code is tested on SheetJS v0.14.0 -->
<template>
  <button type="button" v-on:click="onexport">Excel download</button>
</template>

<script>
import XLSX from 'xlsx'

export default {
  data: () => ({
    Datas: {
      // We will make a Workbook contains 2 Worksheets
      'animals': [
                  {"name": "cat", "category": "animal"}
                  ,{"name": "dog", "category": "animal"}
                  ,{"name": "pig", "category": "animal"}
                ],
      'pokemons': [
                  {"name": "pikachu", "category": "pokemon"}
                  ,{"name": "Arbok", "category": "pokemon"}
                  ,{"name": "Eevee", "category": "pokemon"}
                ]
    }
  }),
  methods: {
    onexport () { // On Click Excel download button
    
      // export json to Worksheet of Excel
      // only array possible
      var animalWS = XLSX.utils.json_to_sheet(this.Datas.animals) 
      var pokemonWS = XLSX.utils.json_to_sheet(this.Datas.pokemons) 

      // A workbook is the name given to an Excel file
      var wb = XLSX.utils.book_new() // make Workbook of Excel

      // add Worksheet to Workbook
      // Workbook contains one or more worksheets
      XLSX.utils.book_append_sheet(wb, animalWS, 'animals') // sheetAName is name of Worksheet
      XLSX.utils.book_append_sheet(wb, pokemonWS, 'pokemons')   

      // export Excel file
      XLSX.writeFile(wb, 'book.xlsx') // name of the file is 'book.xlsx'
    }
  }
}
</script>
```

End!

## Reference

Document for XLSX.utils: https://github.com/SheetJS/js-xlsx#utility-functions

[SheetJS]: https://sheetjs.com/
[Vue.js]: https://vuejs.org/