<template>
    <div>
      <input type="file" @change="handleChange">
    </div>
</template>

<script>
    export default {
        name: "uploadTest",
      data(){
          return {

          }
      },
      methods:{
          handleChange(file){
            console.log('aaa');
            // this.$emit('on-change',file);
          }
      }
    }
</script>

<style scoped>

</style>
