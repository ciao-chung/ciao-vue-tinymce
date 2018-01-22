# Ciao Vue Tinymce

## Feature

* Easy for use just bind v-model
* Language can be customize
* Can be photo upload and custom uploaded image tag
* Can catch photo upload Event

## Installation

> yarn add git+https://github.com/ciao-chung/ciao-vue-tinymce.git

This is temporarily install way

After I Finish First Version

I will publish on npm

## Base Usage

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
```

## Property

### language (optional)

> String

To specify language

Should set language code by this property 
 
The language code of tinymce can reference [this](https://www.tinymce.com/docs/configure/localization/#language)

When use language option

You need download [Language Package](https://www.tinymce.com/download/language-packages/)

And set [language_url](https://www.tinymce.com/docs/configure/localization/#language_url) for tinymce

**Example**
```html
<Tinymce v-model="data" :lanuage="'zh_TW'" :language="http://foo.bar/static/langs/zh_TW.js" />
```

### photoUploadRequest (optional)

> Function

This property is a function type

And it should return a **[Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)**

Because **Ciao Vue Tinymce** can make sure upload request is finished via use **[await](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/await)** in this way



In addition, this function must given a **file** argument

Let request can send the file to upload

**Example(Template)**
```html
<Tinymce v-model="data" :photoUploadRequest="uploadApi" />
```

**Example(Script)**
```javascript
export default {
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
}
```

If you don't custom **photoUploadTag** property

Your upload photo response should be json format

And this json must been had **url** property

**Example(Upload Photo Response Data)**
```javascript
{
  url: 'https://vuejs.org/images/logo.png'
}
```

### photoUploadTag (optional)

> Function

After upload photo

**Ciao Vue Tinymce** will insert image tag into editor

For example, if upload response is **{ url: 'https://vuejs.org/images/logo.png' }**

By default, **Ciao Vue Tinymce** will create default image tag **&lt;img src="https://vuejs.org/images/logo.png" /&gt;**

If you want custom image tag

You can replace it as follows

Just create a function and given a **result** argument(result is upload response)

In this way, you can make image tag whatever you want

**Example**
```javascript
export default {
  methods: {
    resposneImage: function (result) {
      return `<img src="${result.url}" class="img-responsive" />`
    }
  },
}
```

**Full Example**
```html
<template>
  <div>
    <Tinymce
      :photoUploadRequest="uploadApi"
      :photoUploadTag="resposneImage"
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
    resposneImage: function (result) {
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