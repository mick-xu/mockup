<template>
  <div id="app">
    <div id="nav"></div>
    <router-view />
  </div>
</template>

<style lang="scss">
html,
body {
  margin: 0;
  padding: 0;
  #app {
    position: relative;
    height: 100vh;
  }
}

#nav {
  padding: 30px;
}
</style>
