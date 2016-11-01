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
      uploadFileLength: 0, //已经上传文件的数量
      getopidanduploadurl: false //上传前获得异步上传地址
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
    },
    uploadurl: {        //传到服务器后台的url必传
      type: String
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
          //监听reader加载完成的事件   注意闭包问题
          reader.onload = (function (nowFile, content) {
            return function (e) {
              let file = e.target.result;  //base64
              content.uploaderHandle(nowFile, file);  
            };
          })(nowFile, this);
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
    },
    uploaderHandle (nowFile) {
      //因为垃圾后台不支持多张图片上传 那么就只好一张张的上传咯 😡
      //等待前台的上传地址加载完成后才能运行真正的上传
      if(this.uploadurl) {
        let fm = new window.FormData();
        fm.append('files[]'+ String(Math.random()).substring(3,6), nowFile);
        //正儿八经的上传
        window.fetch(this.uploadurl, {
          body:fm,
          method: 'POST'
        }).then(data => {
            if(data.status >=200 && data.status < 300 && data.statusText === 'OK') {
              return Promise.resolve(data.json());
            }else {
               throw Error(data.statusText);
            }
        }).then(data => {
          console.log(data);
        }).catch(err => {
          console.log(err);
          alert('网络错误');
        })
      }else {
        alert('网络有点异常，请稍后重试');
        return false;
      }
    }
  },
  watch: {
    //watch来观察传进来的异步 数据
    uploadurl (n) {
      n ? this.uploadurl = n : null;
    }
  }
}
</script>

