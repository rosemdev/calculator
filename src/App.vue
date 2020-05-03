n<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <p>@rosem</p>
    <calculator/>
  </div>
</template>

<script>
import Calculator from "./components/Calculator";

export default {
  name: 'App',
  components: {
    Calculator,
  }
}
</script>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Work+Sans:wght@100;300;400&display=swap');
  @import "styles/button_reset.css";
</style>
<style>
  body {
    background: linear-gradient(90deg, rgba(55,64,74,1) 15%, rgba(228,38,144,1) 100%);
    /*background: linear-gradient(90deg, rgba(55,64,74,1) 0%, rgba(29,108,189,1) 50%, rgba(228,38,144,1) 100%);*/
  }

#app {
  font-family: 'Work Sans', sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
