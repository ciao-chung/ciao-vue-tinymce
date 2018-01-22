# Ciao Vue Tinymce

> A Vue 2 Tinymce component

![](https://github.com/ciao-chung/ciao-vue-tinymce/blob/master/static/photo/demo.gif?raw=true)

## Feature

* Easy for use just bind v-model
* Language can be customize
* Can be photo upload and custom uploaded image tag
* Can catch photo upload Event

## Prerequisites

* Vue app has babel-loader
* Vue app has sass-loader 

## Installation

> npm install ciao-vue-tinymce

or yarn

> yarn add ciao-vue-tinymce

## Base Usage

Just use **v-model** bind value

When editor on blur will auto sync v-model value

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

### language

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

### photoUploadRequest

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

### photoUploadTag

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

### progress

> Boolean

If **photoUploadRequest** has been setup

By default, when photo start upload will show bootstrap progress bar

If you wanna hide progress bar

You can set progress as false 

## Event

### blur

This event will be emitted when editor on blur

And this event will pass editor content as an argument

**Example(Template)**
```html
<template>
  <div>
    <Tinymce @blur="onBlur" v-model="data"/>
  </div>
</template>
```

**Example(Script)**
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

This event will be emitted when editor on change

And this event will pass editor content as an argument

**Example(Template)**
```html
<template>
  <div>
    <Tinymce @change="onChange" v-model="data"/>
  </div>
</template>
```

**Example(Script)**
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

This event will be emitted when **photoUploadRequest** resolve(success) Promise

And this event will pass upload response result as an argument

**Example(Template)**
```html
<template>
  <div>
    <Tinymce
      @uploadSuccess="onUploadSuccess"
      :photoUploadRequest="uploadApi"
      :photoUploadTag="resposneImage"
      v-model="data"/>
  </div>
</template>
```

**Example(Script)**
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

This event will be emitted when **photoUploadRequest** reject(error) Promise

And this event will pass upload response error as an argument

**Example(Template)**
```html
<template>
  <div>
    <Tinymce
      @uploadFail="uploadFail"
      :photoUploadRequest="uploadApi"
      :photoUploadTag="resposneImage"
      v-model="data"/>
  </div>
</template>
```

**Example(Script)**
```javascript
export default {
  methods: {
    uploadFail: function(error) {
      // TODO
    }
  },
}
```