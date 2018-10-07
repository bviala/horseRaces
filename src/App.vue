<template>
  <div id="app">
    <img src="./assets/horseLogo.png">
    <horse-races></horse-races>
  </div>
</template>

<script>
import HorseRaces from './components/HorseRaces.vue'

export default {
  name: 'app',
  components: {
    HorseRaces
  }
}
</script>

<style>
@import '~normalize.css';
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
#app img {
  width: 100px;
  height: auto;
}
body {
    margin-left: 200px;
    margin-right: 200px;
}
@media only screen and (max-width: 600px) {
  body {
    margin: 30px;
  }
}
</style>
