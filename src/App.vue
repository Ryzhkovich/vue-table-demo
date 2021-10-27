<template>
  <img alt="Vue logo" src="./assets/logo.png">
  <TableComponentTest1 />
</template>

<script>
import TableComponentTest1 from "./components/TableComponent/TestsComponents/TableComponentTest1";

export default {
  name: 'App',
  components: {
    TableComponentTest1,
  }
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
</style>
