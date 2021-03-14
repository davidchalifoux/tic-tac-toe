<template>
  <h1>Tic-Tac-Toe</h1>
  <Board />
</template>

<script>
import Board from "./components/Board.vue";

export default {
  name: "App",
  components: {
    Board,
  },
};
</script>

<style>
body {
  background-color: #1d1d1d;
  color: #eeeeee;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  margin-top: 60px;
  display: flex;
  flex-direction: column;
  align-items: center;
}
</style>
