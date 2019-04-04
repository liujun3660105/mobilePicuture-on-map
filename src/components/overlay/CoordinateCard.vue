<template>
  <div class="ol-coordinateCard">
    <div class="popup-content">
      <span>经度：{{position[0]}}</span>
      <br>
      <span>纬度：{{position[1]}}</span>
    </div>
  </div>
</template>

<script>
  export default {
    name: "CoordinateCard",
    props:{
      position:{
        type:Array,
        required:true
      }
    }
  }
</script>

<style scoped>
  .ol-coordinateCard{
    z-index:5;
    position:absolute;
    top:2px;
    right:20px;
    background:white;
    font-color:black;
  }

</style>
