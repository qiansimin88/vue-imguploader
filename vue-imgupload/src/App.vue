<template>
  <div id="app">
    <imguploader :uploadurl = 'uploadurl' :file-max-length = '3' accept="image/jpeg,image/png" :single-file-size = '930000'></imguploader> 
    <a href="javascript:void(0);" @click="upload">呵呵</a>
  </div>
</template>

<script>
import imguploader from './components/imguploader'

export default {
  name: 'app',
  data () {
    return {
      uploadurl: 'http://www.3dker.cn/japi/dev/upload/imgUpload?accessOpenId=450958219a8349b1aab39257d1efe647&language=zh-CN&userId=%2256f2405c9c774e1d00f9f0d8%22'
    }
  },
  components: [
    imguploader
  ],
  methods: {
    upload () {
      this.$broadcast('upload');
    }
  }
}
</script>
<style>
* {
  margin: 0; padding: 0;
}
input {
  border: 0; outline: 0;
}
html {
  height: 100%;
}

body {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100%;
}
</style>
