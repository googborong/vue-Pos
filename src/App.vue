<template>
  <div id="app">
    <Pos></Pos>
    <div class="main">
      <router-view></router-view>
    </div>
  </div>
</template>

<script>
import Pos from "@/components/common/leftNav";
export default {
  name: "app",
  components: {
    Pos
  }
};
</script>

<style>
#app {
  font-family: "Microsoft YaHei", "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: left;
  color: #2c3e50;
  height: 100%;
}
.main {
  float: left;
  width: 95%;
  background-color: #eff2f7;
  height: 100%;
  overflow: auto;
}
</style>
