<template>
  <div class="pic-upload-less">
    <form id="imgUploadLess" method="post" action="http://upload.qiniu.com/"
          enctype="multipart/form-data"  style="display:none;">
      <input name="key" type="hidden" value="<resource_key>" v-model="form.key">
      <input name="x:<custom_name>" type="hidden" value="<custom_value>">
      <input name="token" type="hidden" value="" v-model="form.token">
      <input name="file" type="file" id="uploadImgLess"/>
      <!--<input name="crc32" type="hidden"/>-->
      <input name="accept" type="hidden"/>
      <input type="submit">
    </form>
  </div>
</template>

<script>
  import bus from '../../../assets/bus'
  export default {
    name: 'pic-upload-less',
    data () {
      return {
        imgKeys: [],
        form: {
          token: '',
          key: ''
        },
        imgList: []
      }
    },
    mounted: function () {
      var that = this;
      $('input#uploadImgLess[name=file][type=file]').change(function (e) {
        if($(this).val()=='') return;
        that.uploadImg(this.files[0].name)
      });
    },
    methods: {

      uploadImg(name){
        var userInfo = JSON.parse(sessionStorage.userInfo);
        var that = this;
        that.form.key = 'temp' + userInfo.phoneNum + (new Date().getTime() + '.jpg');
        this.$http.get(globalPath+'/GetQiNiuToken?name=' + that.form.key).then(function (res) {

          that.form.token = res.body;

          setTimeout(function () {
            $('#imgUploadLess').ajaxSubmit(function (res) {
              /*var obj = {url: 'http://orbi0d8g8.bkt.clouddn.com/' + res.key};
              that.imgList.push(obj);
              that.imgKeys.push(res.key);
              that.$emit('imgKeys',that.imgKeys);*/
              that.$emit('url',res.key);

            });
          }, 300);

        });
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .col_reset {
    /*padding:10px;*/
  }

  .img_box {
    border-radius: 5px;
    overflow: hidden;
    margin-bottom: 10px;
  }

  .upload_box {
    width: 100%;
    border: 1px dashed #999;
    border-radius: 5px;

    cursor: pointer;
  }

  .upload_box img {
    width: 100%;
  }

  .default_text {
    position: absolute;
    left: 50%;
    top: 50%;
    -webkit-transform: translate(-50%, -50%);
    -moz-transform: translate(-50%, -50%);
    -ms-transform: translate(-50%, -50%);
    -o-transform: translate(-50%, -50%);
    transform: translate(-50%, -50%);
    display: block;
    font-size: 4rem;
    color: #999;
  }
</style>
