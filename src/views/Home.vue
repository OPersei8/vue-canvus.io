<template>
  <div class="home">
    <HelloWorld/>
  <!-- <button v-on:click="show = !show">
    Toggle
  </button> -->
    <!-- <imageTrace /> -->
    <!-- <p v-if="show">hello</p> -->
  </div>
</template>

<script>
// @ is an alias to /src
import HelloWorld from '@/components/HelloWorld.vue'
// import imageTrace from '@/components/imageTrace.vue'

export default {
  name: 'Home',
  components: {
    HelloWorld
    // imageTrace
  },
    data:function(){
        return{
        }
  },
}
</script>
<style scoped>
.home{
  margin:0;
}

#logo{
  animation: fadein 2s linear;
}

@keyframes fadein{
  0% {
    opacity: 0;
    /* transform: scale(.8); */
  }
  50%{
    opacity: 1;
  }
  100% {
    opacity: .5;
    /* transform: scale(1); */
  }
}


</style>