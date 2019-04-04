<template>
  <div class="layout">
    <div class="left">
      <UploadPicture></UploadPicture>

<!--<Upload @on-change="handleChange" action="">-->
  <!--<Button icon="ios-cloud-upload-outline">Upload files</Button>-->
<!--</Upload>-->
      <!--<UploadTest ></UploadTest>-->

    </div>
    <div class="right">
      <Map></Map>
    </div>

    <!--<Layout>-->
      <!--<Header class="main-header">Header</Header>-->
      <!--<Layout class="main-body">-->

  <!--<Sider  >-->
    <!--<UploadPicture></UploadPicture>-->
  <!--</Sider>-->
          <!--<Content>-->
            <!--<Map></Map>-->
          <!--</Content>-->


      <!--</Layout>-->
      <!--<Footer class="main-footer">Footer</Footer>-->
    <!--</Layout>-->

  </div>
</template>

<script>
  import Map from './mainbody/Map'
  import UploadPicture from './mainbody/UploadPicture'
  import UploadTest from './overlay/uploadTest'
    export default {
        name: "Main",
      components:{
          Map,
        UploadPicture,
        UploadTest
      },
      methods:{
        handleChange(data){
          console.log('aaa');
          console.log(data);
        }
      }
    }
</script>

<style scoped>
  .layout{
    /*overflow: hidden;*/
    height:100%;
  }
.left{
  overflow:auto;
  width:10%;
  float:left;
  background-color: #babbad;
  height:100%;

}
  .right{
    width:90%;
    float:right;
    height:1000px;
    height:100%;
  }


</style>
