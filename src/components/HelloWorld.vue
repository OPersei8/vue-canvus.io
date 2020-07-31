<template>
  <div class="hello">
    <!-- <img class="a logo uno dos tres name" src="../assets/logo.png"/> -->
    <a class="container" href="https://netlify.com" target="_blank"> 
    <div class="logo">
      <div class="uno"></div>
      <div class="dos"></div>
      <div class="tres"></div>
    </div>
    <div class="name">Prettier</div>
</a>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  props: {
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">

a {
  text-decoration: none;
  color: white;
}

.container {
  margin: 20px auto;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  width: 500px;
}

.logo {
  position: relative;
  width: 360px;
  height: 520px;
  animation: 4s fadein;
  .uno, .dos, .tres {
    position: absolute;
    width: 120px;
    height: 100%;
    background: #b00612;
  }
  .uno {
    left: 0;
  }
  .dos {
    background: #e50815;
    left: 0;
    z-index: 1;
    transform-origin: top left;
    transform: skewX(25.4deg);
    box-shadow: 0 0 20px black;
  }
  .tres {
    right: 0;
  }
  &::before {
    display: block;
    content: '';
    position: absolute;
    left: 50%;
    bottom: 0;
    transform: translateX(-50%);
    z-index: 10;
    width: 120%;
    height: 30px;
    background: black;
    border-top-left-radius: 50% 100%;
    border-top-right-radius: 50% 100%;
  }
}

.name {
  text-transform: uppercase;
  letter-spacing: 15px;
  animation: 4s name linear;
}

@keyframes fadein {
  0% {
    opacity: 0;
    transform: scale(.8);
  }
  100% {
    opacity: 1;
    transform: scale(1);
  }
}

@keyframes name {
  0% {
    opacity: 0;
    transform: scale(.8);
  }
  80% {
    opacity: 0;
    transform: scale(.8);  
  }
  100% {
    opacity: 1;
    transform: scale(1);
  }
}
</style>
