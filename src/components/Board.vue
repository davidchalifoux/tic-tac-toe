<template>
  <div id="board">
    <div class="box" @click="play(0, 0)">{{ board[0][0] }}</div>
    <div class="box" @click="play(0, 1)">{{ board[0][1] }}</div>
    <div class="box" @click="play(0, 2)">{{ board[0][2] }}</div>
    <div class="box" @click="play(1, 0)">{{ board[1][0] }}</div>
    <div class="box" @click="play(1, 1)">{{ board[1][1] }}</div>
    <div class="box" @click="play(1, 2)">{{ board[1][2] }}</div>
    <div class="box" @click="play(2, 0)">{{ board[2][0] }}</div>
    <div class="box" @click="play(2, 1)">{{ board[2][1] }}</div>
    <div class="box" @click="play(2, 2)">{{ board[2][2] }}</div>
  </div>
  <p v-if="!winner">Turn: {{ currentPlayer }}</p>
  <p v-if="winner">Winner: {{ currentPlayer }}</p>
</template>

<script>
export default {
  name: "Board",
  data() {
    return {
      board: [
        [null, null, null],
        [null, null, null],
        [null, null, null],
      ],
      currentPlayer: null,
      winner: null,
    };
  },
  computed: {},
  methods: {
    play(rowIndex, boxIndex) {
      if (!this.checkWinner()) {
        if (this.board[rowIndex][boxIndex] === null) {
          this.board[rowIndex][boxIndex] = this.currentPlayer;

          if (this.checkWinner()) {
            this.winner = this.currentPlayer;
          } else {
            // Rotate current player
            if (this.currentPlayer == "O") {
              this.currentPlayer = "X";
            } else {
              this.currentPlayer = "O";
            }
          }
        }
      }
    },
    checkWinner() {
      let board = this.board;

      // Vertical, leftmost
      if (
        board[0][0] !== null &&
        board[0][0] === board[1][0] &&
        board[1][0] === board[2][0]
      ) {
        return true;
      }

      // Vertical, middle
      if (
        board[0][1] !== null &&
        board[0][1] === board[1][1] &&
        board[1][1] === board[2][1]
      ) {
        return true;
      }

      // Vertical, rightmost
      if (
        board[0][2] !== null &&
        board[0][2] === board[1][2] &&
        board[1][2] === board[2][2]
      ) {
        return true;
      }

      // Horizontal, top
      if (
        board[0][0] !== null &&
        board[0][0] === board[0][1] &&
        board[0][1] === board[0][2]
      ) {
        return true;
      }

      // Horizontal, middle
      if (
        board[1][0] !== null &&
        board[1][0] === board[1][1] &&
        board[1][1] === board[1][2]
      ) {
        return true;
      }

      // Horizontal, bottom
      if (
        board[2][0] !== null &&
        board[2][0] === board[2][1] &&
        board[2][1] === board[2][2]
      ) {
        return true;
      }

      // Diagonal, left-to-right
      if (
        board[0][0] !== null &&
        board[0][0] === board[1][1] &&
        board[1][1] === board[2][2]
      ) {
        return true;
      }

      // Diagonal, right-to-left
      if (
        board[0][2] !== null &&
        board[0][2] === board[1][1] &&
        board[1][1] === board[2][0]
      ) {
        return true;
      }

      // No winner
      return false;
    },
  },
  mounted() {
    const players = ["O", "X"];
    this.currentPlayer = players[Math.floor(Math.random() * 2)];
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#board {
  display: grid;
  justify-items: center;
  align-items: center;
  grid-template-columns: 100px 100px 100px;
  grid-template-rows: 100px 100px 100px;
  border-color: #eeeeee;
  grid-gap: 2px;
  user-select: none;
}
.box {
  background-color: #171717;
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100%;
  width: 100%;
}
.box:hover {
  cursor: pointer;
}
</style>
