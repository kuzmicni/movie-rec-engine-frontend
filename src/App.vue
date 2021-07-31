<template>
  <div id="app">
    <SearchAutocomplete/>
  </div>
</template>

<script>
import SearchAutocomplete from './components/SearchAutocomplete.vue'

export default {
  name: 'App',
  components: {
    SearchAutocomplete
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
  margin-top: 70px;
}
</style>
