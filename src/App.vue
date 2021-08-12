<template>
  <Menu :menuItems="menuItems" />
</template>

<script>
import Menu from "@/components/Menu";
import menuItems from "@/menu.json";
export default {
  name: "App",
  data() {
    return {
      menuItems,
    };
  },
  components: {
    Menu,
  },
};
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
a {
  text-decoration: none;
  color: #2c3e50;
}

* {
  box-sizing: border-box;
}
</style>
