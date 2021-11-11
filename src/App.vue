<template>
  <div id="app" data-app> 
    <div id="nav"> 
      <router-link to="/">Home</router-link> |
      <router-link to="/alunos">Alunos</router-link> |
      <router-link to="/cursos">Cursos</router-link>
    </div>
    <router-view/>
  </div>
</template>


<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
 
}

#name{
  float: left;
}

#nav {
  padding: 30px;
  background-color: #272424;
  border-radius: 2px;

  a {
    font-weight: bold;
    color: #d0e4d8;
    

    &.router-link-exact-active {
      color: #42b983;
    }
  }
}
</style>
