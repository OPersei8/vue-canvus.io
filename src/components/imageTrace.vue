<template>
<div>
  <button @click="show=!show">shake</button>
    <img v-bind:class="{ shake: show }" src="../assets/1.png"/>
    </div>
</template>
<script>
export default {
    data:function(){
      return{
        show:false,
      }
    }
}
</script>
<style scoped>
img {
  /* position: absolute; */
  top: 50%;
  left: 50%;
  width: 75vmin;
  /* -webkit-transform: translate(-50%, -50%);
          transform: translate(-50%, -50%); */
  z-index: 2;
}

.shake{
  animation: shakes .25s infinite;
}

@keyframes shakes {
  0%{
    transform: rotate(0deg); 
  } 
  25%{
    transform: rotate(1.25deg); 
  } 
  50%{
    transform: rotate(0deg); 
  }
  75%{
    transform: rotate(-1.25deg); 
  }
  100%{
    transform: rotate(0deg); 
  }
}
</style>