<template>
  <div id="app">
    <imguploader :file-max-length = '3' accept="image/jpeg,image/png" :single-file-size = '23000'></imguploader> 
    <a href="javascript:void(0);" @click="upload">上传</a>
  </div>
</template>

<script>
import imguploader from './components/imguploader'

export default {
  name: 'app',
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
