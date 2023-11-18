<template>
  <img alt="stark logo" src="./assets/logo.png">
  <StarkDemo msg="Welcome To My Starknet Mainnet Dapp"/>
</template>

<script>
import StarkDemo from './components/StarkDemo.vue'


export default {
  name: 'App',
  components: {
    StarkDemo
  },
}


</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
img {
  width: 160px;
  height: 160px;
}
</style>
