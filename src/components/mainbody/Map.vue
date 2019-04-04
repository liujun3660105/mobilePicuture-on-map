<template>
    <div id="map"  :class="{active:style1}">
      <CoordinateCard :position="coordinate"></CoordinateCard>
      <Popup :imgUrl="imgUrl" :imgName="imgName" :position="showPosition" :map="map"></Popup>
    </div>
</template>

<script>
  import 'ol/ol.css'
  import Map from 'ol/Map';
  import View from 'ol/View';
  import TileLayer from 'ol/layer/Tile';
  import XYZ from 'ol/source/XYZ';
  import {transform} from 'ol/proj'
  import {Bus} from "../../bus/bus";
  import {Vector as VectorLayer} from 'ol/layer.js';
  import {Vector as VectorSource} from 'ol/source.js';
  import Collection from 'ol/Collection';
  import Feature from 'ol/Feature';
  import Point from 'ol/geom/Point'
  import {Circle as CircleStyle, Fill, Stroke, Style} from 'ol/style.js';
  import CoordinateCard from '../overlay/CoordinateCard'
  import Popup from '../overlay/Popup'
  import{bd09togcj02} from 'coortransformall'
    export default {
        name: "Map",
      data(){
          return{
            position:[],
            style1:false,
            coordinate:[],
            map:null,
            vectorLayer:null,
            showPosition:[],
            imgUrl:'',
            imgName:'',
            selectFeature:null
          }
      },
      created(){


      },
      beforeMount(){

      },
      mounted(){
        this.selectFeature=new Feature();
        this.initMap();
        //点击要素，弹出对话框
        var selectFeatureStyle=new Style({
          image:new CircleStyle({
            radius: 10,
            fill:new Fill({
              color:'blue'
            })
          })
        });
        this.map.on('singleclick',(e)=>{
          this.selectFeature.setStyle(null);
          if(this.map.hasFeatureAtPixel(e.pixel)){
            this.selectFeature=this.map.getFeaturesAtPixel(e.pixel)[0];
            this.selectFeature.setStyle(selectFeatureStyle);
            let featureInfo=this.selectFeature.getProperties();
            this.imgUrl=featureInfo.imgUrl;
            this.imgName=featureInfo.name;
            this.showPosition=e.coordinate;
          }
          // console.log(this.map.getFeaturesAtPixel(e.pixel));
          // console.log(e.pixel);
          // console.log(this.vectorLayer.getSource().getFeatures());


        });
        //当鼠标在地图中移动时：1.显示坐标；2.当鼠标触摸到feature时，显示为pointer
        this.map.on('pointermove',(e)=>{
          this.coordinate=e.coordinate;
          if(this.map.hasFeatureAtPixel(e.pixel)){
            this.style1=true;

          }
          else{
            this.style1=false;
          }

        });
        // this.map.on('moveend',()=>{
        //   console.log(this.map.getPixelFromCoordinate([117,39]));
        // });

          Bus.$on('pictureList',(features)=>{
            this.vectorLayer.getSource().clear();
            this.position=features;
            features.forEach((item,index)=>{
              console.log(item.lng,item.lat);
              // let feature=new Feature({
              //   geometry:new Point([item.lng,item.lat])
              // });
              // feature.setProperties({'name':item.name,'imgUrl':item.url});
              // this.vectorLayer.getSource().addFeature(feature);
              // this.map.getView().fit(this.vectorLayer.getSource().getExtent());
              //wgs84转换为火星坐标系，这里不需要，所以注释掉
              let gpsCoorLng=item.lng.toFixed(6);
              let gpsCoorLat=item.lat.toFixed(6);
              console.log(gpsCoorLng);
              let gpsCoordinate=gpsCoorLng+','+gpsCoorLat;
              this.$axios.get('http://restapi.amap.com/v3/assistant/coordinate/convert', {
                params: {
                  key: '347ed66bc7cf31600da83de294096745',
                  locations:gpsCoordinate,
                  coordsys:'gps'
                }
              })
                .then((res) =>{
                  console.log(res.data);
                  let naviAutoCoorLng=res.data.locations.split(',')[0];
                  let naviAutoCoorLat=res.data.locations.split(',')[1];
                  let feature=new Feature({
                    geometry:new Point([naviAutoCoorLng,naviAutoCoorLat])
                  });
                  feature.setProperties({'name':item.name,'imgUrl':item.url});
                  this.vectorLayer.getSource().addFeature(feature);
                  this.map.getView().fit(this.vectorLayer.getSource().getExtent());
                })
                .catch((error)=> {
                  console.log(error);
                });
              // http://restapi.amap.com/v3/assistant/coordinate/convert?key=347ed66bc7cf31600da83de294096745&locations=117.033661,39.927741&coordsys=gps


            });

          });







      },
      components:{
        CoordinateCard,
        Popup
      },
      methods:{
        initMap(){
          var bd09coordinate=[117,39];
          console.log(bd09togcj02(bd09coordinate[0],bd09coordinate[1]));
          this.vectorLayer=new VectorLayer({
            source:new VectorSource(),
            style:new Style({
              image:new CircleStyle({
                radius: 10,
                fill:new Fill({
                  color:'red'
                })
              })
            })
          });
          this.map=new Map({
            target:'map',
            layers: [
              new TileLayer({
                source: new XYZ({
                  // url: 'https://{a-c}.tile.openstreetmap.org/{z}/{x}/{y}.png'
                  url:'http://www.google.cn/maps/vt?lyrs=s@189&gl=cn&x={x}&y={y}&z={z}'
                })
              }),
              this.vectorLayer

            ],
            view: new View({
              center: [117, 39],
              zoom: 10,
              projection:'EPSG:4326',
              maxZoom:20
            })
          });
          // var feature1=new Feature({
          //   geometry:new Point([117,39])
          // });
          // this.vectorLayer.getSource().addFeature(feature1);

        }
      }
    }
</script>

<style scoped>
.active{
  cursor:pointer;
}
  #map{
    height:100%;
  }
</style>
