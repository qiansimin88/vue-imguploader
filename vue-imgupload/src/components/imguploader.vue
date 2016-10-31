<template>
  <div class="uploader">
    <input type="file" :accept="accept" :multiple="multiple" :id="id" @change="changeFile" style="display:none;"> 
  </div>
</template>

<script>
export default {
  name: 'imguploader',
  data () {
    return {
      id: `uploader${Math.random().toString(36).substr(3, 3)}`, // 唯一id
      uploadFileLength: 0 //已经上传文件的数量
    }
  },
  props: {
    multiple: {
      type: Boolean,
      default: true
    },
    accept: {
      type: String,
      default: 'image/*'   // example: 'image/jpeg,image/png'
    },
    singleFileSize: {     // singleFile maxsize oversize
      type: Number,
      default: 5242880 
    },
    fileMaxLength: {     // allfile size that is smart to computed 🙂
      type: Number,
      default: 10 
    }
  },
  events: {
    upload () {
      this.createEventBroadcast();
    }
  },
  ready () {
  },
  methods: {
    // 创建一个鼠标事件用于被父组件触发
    createEventBroadcast () {
      let ev = new window.MouseEvent('click', {
        canceable: false,
        bubble: true
      });
      document.getElementById(this.id).dispatchEvent(ev);
    },
    changeFile (e) {
      let fileslist = e.target.files;
      //通过检测 允许上传
      if(fileslist && this.checkFiles(fileslist)) {
        // 循环所有的上传文件
        for (var i = 0; i < fileslist.length; i++) {
          var nowFile = fileslist[i];
          //js reader方法强制生成base64的字符串
          let reader = new window.FileReader();
          reader.readAsDataURL(nowFile);
          //监听reader加载完成的事件
          reader.addEventListener('load', (e) => {
            let file = e.target.result;
          });
        };
      }
    },
    checkFiles (fileslist) {
      let allowUp = true;
      if(this.uploadFileLength + fileslist.length > this.fileMaxLength) {
        alert('抱歉，您上传的数量超过我们的限制咯');
          return allowUp = false;
      }
      for(var i in fileslist) {
        if(fileslist.hasOwnProperty(i)) {
          let nowFile = fileslist[i];
          if(this.accept.split(',').indexOf(nowFile.type) < 0) {
            alert('抱歉，您上传的文件我们不支持哦');
            return allowUp = false;
          }
          if(nowFile.size > this.singleFileSize) {
            alert('抱歉，您上传的文件体积超过我们的限制');
             return allowUp = false;
          }
        }
      }
      return allowUp;
    }
  }
}
</script>
<style scoped>
h1 {
  color: #42b983;
}
</style>
