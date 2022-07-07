<template>
  <div id="app">
    <HelloWorld msg="Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
import HelloWorld from './views/HomeView.vue'

export default {
  name: 'App',
  components: {
    HelloWorld
  }
}
</script>
