<template>
  <div id="app">
    <BoardCheckers/>
  </div>
</template>

<script>
import BoardCheckers from '@/components/BoardCheckers'

export default {
  name: 'App',
  components: {
    BoardCheckers
  }
}
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
