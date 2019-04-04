
  <template>
    <div>
      <!--<img src="" alt="" ref="img1">-->
      <!--<input type="file" class="file" id="imgUpFile" ref="input">-->
      <!--<button @click="submit">上传</button>-->
      <!--<div>-->
        <!--<ul>-->
          <!--<li v-for=""></li>-->
        <!--</ul>-->
      <!--</div>-->
      

      
      <div class="demo-upload-list" v-for="item in uploadList">
        <template v-if="item.status === 'finished'">
          <img :src="item.url" ref="img">
          <div class="demo-upload-list-cover">
            <Icon type="ios-eye-outline" @click.native="handleView(item.name,item.url)"></Icon>
            <Icon type="ios-trash-outline" @click.native="handleRemove(item)"></Icon>
          </div>
        </template>
        <template v-else>
          <Progress v-if="item.showProgress" :percent="item.percentage" hide-info></Progress>
        </template>
      </div>
      <Upload
        ref="upload"
        :show-upload-list="false"
        :on-success="handleSuccess"
        :format="['jpg','jpeg','png']"
        :max-size="5120"
        :on-format-error="handleFormatError"
        :on-exceeded-size="handleMaxSize"
        :before-upload="handleBeforeUpload"
        :on-process="handleProcess"
        :on-error="handleError"
        @on-change="handleChange"
        multiple
        type="drag"
        action="/static"
        style="display: inline-block;width:100px;">
        <div style="width:100px;height:100px;line-height: 100px; ">
          <Icon type="ios-camera" size="20"></Icon>
        </div>
      </Upload>
      <Modal title="View Image" v-model="visible">
        <img :src="imgUrl" v-if="visible" style="width: 100%">
      </Modal>
      <div>
        <Button @click="showOnMap" :disabled="disabled">提交展示</Button>
        <Button @click="clearAll" :disabled="disabledC">清除所有</Button>
      </div>

    </div>

  </template>
  <script>
    import EXIF from 'exif-js'
    import {Bus} from "../../bus/bus";

    export default {
      data () {
        return {
          defaultList: [
            {
              'name': 'a42bdcc1178e62b4694c830f028db5c0',
              'url': 'https://o5wwk8baw.qnssl.com/a42bdcc1178e62b4694c830f028db5c0/avatar'
            },
            {
              'name': 'bc7521e033abdd1e92222d733590f104',
              'url': 'https://o5wwk8baw.qnssl.com/bc7521e033abdd1e92222d733590f104/avatar'
            }
          ],
          imgName: '',
          visible: false,
          uploadList: [],
          imgUrl:'',
          uploadListPass:[],
          disabled:true,
          disabledC:true
        }
      },
      methods: {
        handleChange(file){
          this.$emit('on-change',file);
        },
        handleView (name,url) {
          this.imgUrl=url;
          this.imgName = name;
          this.visible = true;
        },
        handleRemove (file) {
          //this.$refs.upload.fileList
          const fileList = this.$refs.upload.fileList;
          this.$refs.upload.fileList.splice(fileList.indexOf(file), 1);
        },
        handleProcess(e, file, fileList){
        },
        handleSuccess (res, file) {
          file.url = 'https://o5wwk8baw.qnssl.com/7eb99afb9d5f317c912f08b5212fd69a/avatar';
          file.name = '7eb99afb9d5f317c912f08b5212fd69a';
        },
        handleError(err,file,fileList){
        //建立pictureItem，包含经度、纬度、名称、url、status、percentage
          var item={};
          EXIF.getData(fileList,function(){
            let lng=EXIF.getTag(this,'GPSLongitude')[0]+EXIF.getTag(this,'GPSLongitude')[1]/60+EXIF.getTag(this,'GPSLongitude')[2]/3600;
            let lat=EXIF.getTag(this,'GPSLatitude')[0]+EXIF.getTag(this,'GPSLatitude')[1]/60+EXIF.getTag(this,'GPSLatitude')[2]/3600;
            item.lng=lng;
            item.lat=lat;
            }
          )
         item.name=fileList.name;
         item.status='finished';
         item.percentage=100;
            let reader=new FileReader();
            reader.readAsDataURL(fileList);
            reader.onload=(e)=> {
              item.url = reader.result;
              this.uploadList.push(item);
            }

        },
        handleFormatError (file) {
          this.$Notice.warning({
            title: 'The file format is incorrect',
            desc: 'File format of ' + file.name + ' is incorrect, please select jpg or png.'
          });
        },
        handleMaxSize (file) {
          this.$Notice.warning({
            title: 'Exceeding file size limit',
            desc: 'File  ' + file.name + ' is too large, no more than 2M.'
          });
        },
        // console.log(this.$refs.upload.fileList.length,'beforeupload时上传列表的值');
        handleBeforeUpload (file) {
           // this.$refs.upload.clearFiles();//每一次上传，都清空upload列表
          const check = this.uploadList.length < 10;
          if (!check) {
            this.$Notice.warning({
              title: 'Up to ten pictures can be uploaded.'
            });
          }
          else{
          }
          return check;
        },
        showOnMap(){
          //
          this.uploadListPass=JSON.stringify(this.uploadList);
          this.disabled=true;
          // console.log(this.uploadList);
        },
        clearAll(){
          this.disabledC=true;
          this.uploadList=[];

        }
      },
      mounted () {
        this.uploadList = this.$refs.upload.fileList;
      },
      watch:{
        uploadListPass(newValues){
          let uploadListPassArr=JSON.parse(newValues);
          if(uploadListPassArr.length>0){
            // this.disabled=false;0
            Bus.$emit('pictureList',uploadListPassArr);
          }
          else{
            // this.disabled=false;
          }

        },
        uploadList(newValues){

          if(newValues.length>0){
            this.disabled=false;
            this.disabledC=false;
        //     this.disabled=false;
        //     Bus.$emit('pictureList',newValues);
          }
          else{
            this.disabled=true;
          }
        //
        }
      }
    }
  </script>
  <style>
    .demo-upload-list{
      display: inline-block;
      width: 100px;
      height: 100px;
      text-align: center;
      line-height: 60px;
      border: 1px solid transparent;
      border-radius: 4px;
      overflow: hidden;
      background: #fff;
      position: relative;
      box-shadow: 0 1px 1px rgba(0,0,0,.2);
      margin-right: 4px;
    }
    .demo-upload-list img{
      width: 100%;
      height: 100%;
    }
    .demo-upload-list-cover{
      display: none;
      position: absolute;
      top: 0;
      bottom: 0;
      left: 0;
      right: 0;
      background: rgba(0,0,0,.6);
    }
    .demo-upload-list:hover .demo-upload-list-cover{
      display: block;
    }
    .demo-upload-list-cover i{
      color: #fff;
      font-size: 20px;
      cursor: pointer;
      margin: 0 2px;
    }
  </style>

