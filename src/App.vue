<template>
  <div class="container">
    <Menu>
      <router-link to="/">Home</router-link>
      <router-link to="/about">About</router-link>
      <router-link to="/producto">Productos</router-link>
      <router-link to="/categoria">Categorias</router-link>
    </Menu>
    <div>
      <router-view />
    </div>
  </div>
</template>

<script>

import Menu from './components/Menu.vue'

export default {
  name: 'App',
  data() {
    return {

    }
  },
  components: {
    Menu
  }
}
</script>

<style lang="scss">
.container{
  padding-top: 90px;
}
</style>
