<template>
  <div class="ol-popup" >
    <a href="#" id="popup-closer" class="ol-popup-closer" @click="closePopup()"></a>
    <div class="popup-content">
      <h3>{{imgName}}</h3>
      <div class="popup-content-img">
        <img :src="imgUrl" alt="">
      </div>

    </div>
  </div>
</template>

<script>
  import Overlay from 'ol/Overlay'
    export default {
        name: "Popup",
      data(){
          return{
            overlay:null

          }
      },
      props:{
          imgName:'',
        imgUrl:'',
        position:{
          type:Array,
          require:true
        },
        map:{
            type:Object,
          require:true
        }
      },
      methods:{
          _load(){
            this.overlay=new Overlay({
              element:this.$el,
              autoPan:true,
              autoPanAnimation:{
                duration:250
              },
              position:this.getPosition(this.position)
            });
            console.log(this.map);
            this.map.addOverlay(this.overlay);

          },
        showPopup(position){
          this.overlay.setPosition(position);


        },
        closePopup(){
          this.overlay.setPosition(undefined);

        },
        getPosition(position){
          if(position.length==0){
            return undefined;
          }
          else{
            return position;
          }
        }
      },
      watch:{
          position(newPosition){
            if(this.overlay){
              this.showPopup(newPosition);
            }
            else{
              this._load();
            }
          }
      }
    }
</script>

<style scoped>
  .ol-popup-closer{
    text-decoration: none;
    position:absolute;
    top:2px;
    right:8px;
  }
  .ol-popup-closer:after{
    content:'×'
  }
  .ol-popup{
    background-color:white;
    position:absolute;
  }
  .popup-content-img{
    width:200px;
    height:auto;
  }
  .popup-content-img img{
    width:100%;
  }
</style>
