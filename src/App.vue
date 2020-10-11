<template>
  <div id="app">
    <tree></tree>
    <div class="note" ></div>
  </div>
</template>

<script>
import tree from "./components/tree";
export default {
  name: "App",
  components: {
    tree,
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e55;
  margin-top: 10px;
   background-color: pink;
}

</style>
