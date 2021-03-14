<template>
  <div id="board">
    <div
      class="box"
      :class="{ red: board[0][0] == 'X', blue: board[0][0] == 'O' }"
      @click="play(0, 0)"
    >
      {{ board[0][0] }}
    </div>
    <div
      class="box"
      :class="{ red: board[0][1] == 'X', blue: board[0][1] == 'O' }"
      @click="play(0, 1)"
    >
      {{ board[0][1] }}
    </div>
    <div
      class="box"
      :class="{ red: board[0][2] == 'X', blue: board[0][2] == 'O' }"
      @click="play(0, 2)"
    >
      {{ board[0][2] }}
    </div>
    <div
      class="box"
      :class="{ red: board[1][0] == 'X', blue: board[1][0] == 'O' }"
      @click="play(1, 0)"
    >
      {{ board[1][0] }}
    </div>
    <div
      class="box"
      :class="{ red: board[1][1] == 'X', blue: board[1][1] == 'O' }"
      @click="play(1, 1)"
    >
      {{ board[1][1] }}
    </div>
    <div
      class="box"
      :class="{ red: board[1][2] == 'X', blue: board[1][2] == 'O' }"
      @click="play(1, 2)"
    >
      {{ board[1][2] }}
    </div>
    <div
      class="box"
      :class="{ red: board[2][0] == 'X', blue: board[2][0] == 'O' }"
      @click="play(2, 0)"
    >
      {{ board[2][0] }}
    </div>
    <div
      class="box"
      :class="{ red: board[2][1] == 'X', blue: board[2][1] == 'O' }"
      @click="play(2, 1)"
    >
      {{ board[2][1] }}
    </div>
    <div
      class="box"
      :class="{ red: board[2][2] == 'X', blue: board[2][2] == 'O' }"
      @click="play(2, 2)"
    >
      {{ board[2][2] }}
    </div>
  </div>
  <p class="button" @click="toggleAI()">
    AI: <span v-if="aiToggle">on</span><span v-if="!aiToggle">off</span>
  </p>
  <p v-if="!winner">Turn: {{ currentPlayer }}</p>
  <p v-if="winner">{{ currentPlayer }} wins!</p>
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
      humanPlayer: null,
      winner: null,
      aiToggle: true,
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

            // Check if AIs turn
            if (this.aiToggle && this.currentPlayer != this.humanPlayer) {
              this.playAI();
            }
          }
        }
      }
    },
    playAI() {
      /**
       * Shuffles array in place.
       * @param {Array} a items An array containing the items.
       */
      function shuffle(a) {
        var j, x, i;
        for (i = a.length - 1; i > 0; i--) {
          j = Math.floor(Math.random() * (i + 1));
          x = a[i];
          a[i] = a[j];
          a[j] = x;
        }
        return a;
      }

      let playable = [];
      for (let row = 0; row < this.board.length; row++) {
        for (let column = 0; column < this.board[row].length; column++) {
          if (this.board[row][column] === null) {
            playable.push([row, column]);
          }
        }
      }
      shuffle(playable);
      let move = playable[0];
      this.play(move[0], move[1]);
    },
    toggleAI() {
      this.aiToggle = !this.aiToggle;
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
    this.humanPlayer = this.currentPlayer;
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
.button {
  user-select: none;
}
.button:hover {
  cursor: pointer;
  text-decoration: underline;
}
.red {
  color: #ff4600;
}
.blue {
  color: #7293fe;
}
.box:hover {
  cursor: pointer;
}
</style>
