<template>
  <div id="app">
    <router-view/>
  </div>
</template>

<style lang="scss">

html, body, #app {
  height: 100%;
  width: 100%;
  margin: 0;
}

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

body {
  background-color: #377EB7;
}

input, button {
  color: #2c3e50;
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  font-size: 1em;
  margin: 2px;
  border-radius: 2px;
  border: 3px solid #386e9a;
  padding: 5px 15px;
}

button {
  background-color: lighten(#386e9a, 20%);
  font-weight: bold;
  color: white;
}

#nav {
  padding: 30px;
  a {
    font-weight: bold;
    color: #2c3e50;
    &.router-link-exact-active {
      color: #42b983;
    }
  }
}
</style>
