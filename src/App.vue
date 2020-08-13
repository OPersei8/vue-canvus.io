<template>
    <router-view/>
</template>

<style lang="scss">
body {
  animation: fadein 1s linear;
  margin: 0;
  // color: white;
  // font-family: "Arial", sans-serif;
  // font-size: 45px;
  // font-weight: bold;
}

@keyframes fadein {
  0%{
    background-color: rgb(128, 128, 128);
  }
}
</style>
