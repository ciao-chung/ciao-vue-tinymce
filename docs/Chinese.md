# Ciao Vue Tinymce

> Vue 2 Tinymce元件

![](https://github.com/ciao-chung/ciao-vue-tinymce/blob/master/static/photo/demo.gif?raw=true)

## 主要功能

* 只要使用v-model就能綁定
* 能夠自訂語言
* 能夠使用圖片上傳, 以及自訂上傳後要插入的圖片標籤
* 能夠取得圖片上傳的事件

## 安裝

> npm install ciao-vue-tinymce

使用yarn

> yarn add ciao-vue-tinymce

## 基本使用方式

只要使用 **v-model** 綁定即可

當編輯器在blur時將會自動同步v-model的值

```html
<template>
  <div>
    <Tinymce v-model="data"/>
  </div>
</template>

<script>
import Tinymce from 'ciao-vue-tinymce'
export default {
  data: function() {
    return {
      data: null,
    }
  },
  components: {
    Tinymce,
  },
}
</script>

<style src="bootstrap/dist/css/bootstrap.min.css"></style>
<style src="tinymce/skins/lightgray/skin.min.css"></style>
<style src="tinymce/skins/lightgray/content.min.css"></style>
<style src="ciao-vue-tinymce/dist/dist.css"></style>
```

## 屬性

### tools

> Array

你能夠利用這個屬性經由[addButton](https://www.tinymce.com/docs/api/tinymce/tinymce.editor/#addbutton)將tinymce新增多個自訂的按鈕

透過設定一個callback function給**onclick**屬性

你將能得到一個**editor**參數

並且利用這個**editor**參數來操作**tinymce編輯器實體**


**範例**

```html
<template>
  <div>
    <Tinymce v-model="data" :tools="tools"/>
  </div>
</template>

<script>
import Tinymce from 'ciao-vue-tinymce'
export default {
  data: function() {
    return {
      data: null,
      tools: [
        {
          toolbar: 'foo',
          text: 'foo',
          icon: 'image',
          onclick: (editor) => console.warn('onclick', editor)
        },
        {
          toolbar: 'bar',
          text: 'bar',
          image: 'https://cdnjs.cloudflare.com/ajax/libs/ionicons/4.0.0-9/svg/logo-google.svg',
          onclick: (editor) => console.warn('onclick', editor)
        },
      ]
    }
  },
  methods: {
    onFooButtonClick: function(editor) {
      // TODO
      editor.insertContent('click foo button')
    },
  },
  components: {
    Tinymce,
  },
}
</script>
```

### language

> String

如果要指定語言必須設定此屬性

Tinymce的語言代碼可以參考[這裡](https://www.tinymce.com/docs/configure/localization/#language)

當使用語言選項時

你必須先下載[語系檔案](https://www.tinymce.com/download/language-packages/)

並且設定Tinymce的[language_url](https://www.tinymce.com/docs/configure/localization/#language_url)參數

**範例**
```html
<Tinymce v-model="data" :lanuage="'zh_TW'" :language="http://foo.bar/static/langs/zh_TW.js" />
```

### photoUploadRequest

> Function

此屬性必須為Function格式

而且必須回傳一個[Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)

因為這樣才能讓**Ciao Vue Tinymce**透過[await](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/await)的方式

確認上傳圖片的請求已經完成


此外, 這個function必須給予**file**及**onProgress**這兩個參數

讓上傳請求能夠上傳檔案

**Example(Template)**
```html
<Tinymce v-model="data" :photoUploadRequest="uploadApi" />
```

**Example(Script)**
```javascript
export default {
  computed: {
    uploadApi: function(file, onProgress) {
      return new Promise((resolve, reject) => {
        $.ajax({
          url: 'http://foo.bar/path/to/upload',
          jsonDataRequest: false,
          processData: false,
          contentType: false,
          data: file,
          success: (data) => { resolve(data) },
          error: (error) => { reject(error) },
        })
      })
    },
  },
}
```

此上傳功能也支援上傳進度條(Progress Bar)

如果要啟用顯示進度功能

只要在上傳請求中加入progress event listener

並且傳入一個進度百分比的參數給**onProgress** funciton

**範例(Script)**
```javascript
export default {
  computed: {
    uploadApi: function(file, onProgress) {
      return new Promise((resolve, reject) => {
        $.ajax({
          url: 'http://foo.bar/path/to/upload',
          jsonDataRequest: false,
          processData: false,
          contentType: false,
          data: file,
          xhr: () => {
            let xhr = $.ajaxSettings.xhr()
            xhr.upload.addEventListener('progress', (progress) => {
              const percentage = Math.floor(100*(progress.loaded/progress.total))
              return onProgress(percentage)
            }, false)
            return xhr
          },
          success: (data) => { resolve(data) },
          error: (error) => { reject(error) },
        })
      })
    },
  },
}
```

如果你沒有要自訂**photoUploadTag**屬性

你的上傳圖片response必須是json格式

而且這個json格式必須包含一個**url**屬性

**範例(上傳圖片response資料格式)**
```javascript
{
  url: 'https://vuejs.org/images/logo.png'
}
```

### photoUploadTag

> Function


在上傳完圖片後

**Ciao Vue Tinymce**將會在編輯器中插入圖片標籤

假設上傳的response為 **{ url: 'https://vuejs.org/images/logo.png' }**

在預設的狀況下, **Ciao Vue Tinymce**將會建立一個預設的圖片標籤&lt;img src="https://vuejs.org/images/logo.png" /&gt;

如果你希望自訂這個標籤

你可以依照下列方式自訂圖片標籤

只要建立一個function並且給予一個**result**參數(result就是上傳請求的response)

透過這個方式

你就能自訂任何你要的圖片標前

**範例**
```javascript
export default {
  methods: {
    responseImage: function (result) {
      return `<img src="${result.url}" class="img-responsive" />`
    }
  },
}
```

**完整範例**
```html
<template>
  <div>
    <Tinymce
      :photoUploadRequest="uploadApi"
      :photoUploadTag="responseImage"
      v-model="data"/>
  </div>
</template>

<script>
import Tinymce from 'ciao-vue-tinymce'
export default {
  data: function() {
    return {
      data: null,
    }
  },
  methods: {
    responseImage: function (result) {
      return `<img src="${result.url}" class="img-responsive" />`
    }
  },
  computed: {
    uploadApi: function(file) {
      return new Promise((resolve, reject) => {
        $.ajax({
          url: 'http://foo.bar/path/to/upload',
          jsonDataRequest: false,
          processData: false,
          contentType: false,
          data: file,
          success: (data) => { resolve(data) },
          error: (error) => { reject(error) },
        })
      })
    },
  },
  components: {
    Tinymce,
  },
}
</script>
```

### formDataFilename

> String

在預設的情況下

上傳的**FormData**檔案名稱為**file**(如下)

```javascript
file.append('file', file)
```

如果你希望自訂這個檔名

你可以設定此參數

### config

這個選項允許你替換任何預設的tinymce設定

你可以自行設定任何tinymcy設定

**範例**

```html
<template>
  <div>
    <Tinymce :config="config" v-model="data"/>
  </div>
</template>

<script>
export default {
  data: function() {
   return {
     data: null,
     config: {
       extended_valid_elements: 'img[width|height|id|class|src|uid|extension|version]',
     },
   } 
  }
}
</script>
```

## 事件

### blur

這個事件將會在編輯器blur時被觸發

除此之外此事件還會傳遞一個編輯器內容的參數

**範例(Template)**
```html
<template>
  <div>
    <Tinymce @blur="onBlur" v-model="data"/>
  </div>
</template>
```

**範例(Script)**
```javascript
export default {
  methods: {
    onBlur: function(data) {
      // TODO
    }
  },
}
```

### change

這個事件將會在編輯器內容更動時被觸發

除此之外此事件還會傳遞一個編輯器內容的參數

**範例(Template)**
```html
<template>
  <div>
    <Tinymce @change="onChange" v-model="data"/>
  </div>
</template>
```

**範例(Script)**
```javascript
export default {
  methods: {
    onChange: function(data) {
      // TODO
    }
  },
}
```

### uploadSuccess

這個事件將會在**photoUploadRequest** resolve Promise時(上傳成功時)被觸發

此外這個事件還會傳遞一個為response的result參數

**範例(Template)**
```html
<template>
  <div>
    <Tinymce
      @uploadSuccess="onUploadSuccess"
      :photoUploadRequest="uploadApi"
      :photoUploadTag="responseImage"
      v-model="data"/>
  </div>
</template>
```

**範例(Script)**
```javascript
export default {
  methods: {
    onUploadSuccess: function(data) {
      // TODO
    }
  },
}
```

### uploadFail

這個事件將會在**photoUploadRequest** reject Promise時(上傳失敗時)被觸發

此外這個事件還會傳遞一個為response的error參數

**範例(Template)**
```html
<template>
  <div>
    <Tinymce
      @uploadFail="uploadFail"
      :photoUploadRequest="uploadApi"
      :photoUploadTag="responseImage"
      v-model="data"/>
  </div>
</template>
```

**範例(Script)**
```javascript
export default {
  methods: {
    uploadFail: function(error) {
      // TODO
    }
  },
}
```