<template>
  <div id="app">
    <button @click="show = !show" v-if="!show">play checkers</button>
    <BoardCheckers v-if="show"/>
  </div>
</template>

<script>
import BoardCheckers from '@/components/BoardCheckers'

export default {
  name: 'App',
  components: {
    BoardCheckers
  },
  data: () => ({
    show: true
  })
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
