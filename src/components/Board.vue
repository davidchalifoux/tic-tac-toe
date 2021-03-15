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
  <p class="button" @click="toggleMemoization()" v-if="aiToggle">
    Memoization: <span v-if="memoizationToggle">on</span
    ><span v-if="!memoizationToggle">off</span>
  </p>
  <p class="button" @click="firstMoveAI()" v-if="playCount == 0 && aiToggle">
    AI goes first
  </p>
  <p v-if="!winner">
    {{ currentPlayer }}'s turn
    <span v-if="currentPlayer == humanPlayer">(Human)</span>
    <span v-if="currentPlayer != humanPlayer">(AI)</span>
  </p>
  <p v-if="winner">
    {{ currentPlayer }} wins!
    <span class="button" @click="reset()">Play again?</span>
  </p>
  <p v-if="winner && aiToggle && memoizationToggle">
    Memoization work: {{ memoizationCounter }}
  </p>
  <p v-if="winner && aiToggle && !memoizationToggle">
    Non-memoization work: {{ nonMemoizationCounter }}
  </p>
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
      playCount: 0,
      currentPlayer: null,
      humanPlayer: null,
      winner: null,
      aiToggle: true,
      memoizationToggle: true,
      memoizationCounter: 0,
      nonMemoizationCounter: 0,
    };
  },
  computed: {},
  methods: {
    play(rowIndex, boxIndex) {
      if (!this.checkWinner()) {
        if (this.board[rowIndex][boxIndex] === null) {
          this.board[rowIndex][boxIndex] = this.currentPlayer;
          this.playCount++;

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
              // Delay AI's turn
              setTimeout(() => {
                this.playAI();
              }, 250);
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
        let j, x, i;
        for (i = a.length - 1; i > 0; i--) {
          j = Math.floor(Math.random() * (i + 1));
          x = a[i];
          a[i] = a[j];
          a[j] = x;
        }
        return a;
      }

      // Memoization
      if (this.memoizationToggle) {
        let playable = [];
        for (let row = 0; row < this.board.length; row++) {
          for (let column = 0; column < this.board[row].length; column++) {
            this.memoizationCounter++;
            if (this.board[row][column] === null) {
              playable.push([row, column]);
            }
          }
        }
        shuffle(playable);
        let move = playable[0];
        if (this.checkWinningMove("memoization")) {
          let x = this.checkWinningMove("memoization");
          this.play(x[0], x[1]);
        } else {
          this.play(move[0], move[1]);
        }

        // Non-Memoization
      } else if (!this.memoizationToggle) {
        for (let row = 0; row < this.board.length; row++) {
          for (let column = 0; column < this.board[row].length; column++) {
            this.nonMemoizationCounter++;
            if (this.board[row][column] === null) {
              if (this.checkWinningMove("nonmemoization")) {
                let x = this.checkWinningMove("nonmemoization");
                return this.play(x[0], x[1]);
              } else {
                return this.play(row, column);
              }
            }
          }
        }
      }
    },
    firstMoveAI() {
      // Rotate current player
      if (this.currentPlayer == "O") {
        this.currentPlayer = "X";
      } else {
        this.currentPlayer = "O";
      }

      this.playAI();
    },
    toggleAI() {
      this.aiToggle = !this.aiToggle;
    },
    toggleMemoization() {
      this.memoizationToggle = !this.memoizationToggle;
    },
    reset() {
      this.board = [
        [null, null, null],
        [null, null, null],
        [null, null, null],
      ];
      this.winner = null;
      this.playCount = 0;
      this.memoizationCounter = 0;
      this.nonMemoizationCounter = 0;
      // Same as mounted()
      const players = ["O", "X"];
      this.currentPlayer = players[Math.floor(Math.random() * 2)];
      this.humanPlayer = this.currentPlayer;
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

      // Tie
      let playable = [];
      for (let row = 0; row < this.board.length; row++) {
        for (let column = 0; column < this.board[row].length; column++) {
          if (this.board[row][column] === null) {
            playable.push([row, column]);
          }
        }
      }
      if (playable.length === 0) {
        this.currentPlayer = "Nobody";
        return true;
      }

      // No winner
      return false;
    },
    checkWinningMove(memType) {
      let board = this.board;

      // top left
      if (memType == "memoization") {
        this.memoizationCounter += 9;
      } else if (memType == "nonmemoization") {
        this.nonMemoizationCounter += 9;
      }
      if (
        (board[0][0] == null &&
          board[1][0] === this.currentPlayer &&
          board[2][0] === this.currentPlayer) ||
        (board[0][0] == null &&
          board[0][1] === this.currentPlayer &&
          board[0][2] === this.currentPlayer) ||
        (board[0][0] == null &&
          board[1][1] === this.currentPlayer &&
          board[2][2] === this.currentPlayer)
      ) {
        return [0, 0];
      }

      // top middle
      if (memType == "memoization") {
        this.memoizationCounter += 6;
      } else if (memType == "nonmemoization") {
        this.nonMemoizationCounter += 6;
      }
      if (
        (board[0][1] == null &&
          board[1][1] === this.currentPlayer &&
          board[2][1] === this.currentPlayer) ||
        (board[0][1] == null &&
          board[0][0] === this.currentPlayer &&
          board[0][2] === this.currentPlayer)
      ) {
        return [0, 1];
      }

      // top right
      if (memType == "memoization") {
        this.memoizationCounter += 9;
      } else if (memType == "nonmemoization") {
        this.nonMemoizationCounter += 9;
      }
      if (
        (board[0][2] == null &&
          board[0][0] === this.currentPlayer &&
          board[0][1] === this.currentPlayer) ||
        (board[0][2] == null &&
          board[1][2] === this.currentPlayer &&
          board[2][2] === this.currentPlayer) ||
        (board[0][2] == null &&
          board[1][1] === this.currentPlayer &&
          board[2][0] === this.currentPlayer)
      ) {
        return [0, 2];
      }

      // middle left
      if (memType == "memoization") {
        this.memoizationCounter += 6;
      } else if (memType == "nonmemoization") {
        this.nonMemoizationCounter += 6;
      }
      if (
        (board[1][0] == null &&
          board[0][0] === this.currentPlayer &&
          board[2][0] === this.currentPlayer) ||
        (board[1][0] == null &&
          board[1][1] === this.currentPlayer &&
          board[1][2] === this.currentPlayer)
      ) {
        return [1, 0];
      }

      // middle middle
      if (memType == "memoization") {
        this.memoizationCounter += 12;
      } else if (memType == "nonmemoization") {
        this.nonMemoizationCounter += 12;
      }
      if (
        (board[1][1] == null &&
          board[0][1] === this.currentPlayer &&
          board[2][1] === this.currentPlayer) ||
        (board[1][1] == null &&
          board[1][0] === this.currentPlayer &&
          board[1][2] === this.currentPlayer) ||
        (board[1][1] == null &&
          board[2][0] === this.currentPlayer &&
          board[0][2] === this.currentPlayer) ||
        (board[1][1] == null &&
          board[0][0] === this.currentPlayer &&
          board[2][2] === this.currentPlayer)
      ) {
        return [1, 1];
      }

      // middle right
      if (memType == "memoization") {
        this.memoizationCounter += 6;
      } else if (memType == "nonmemoization") {
        this.nonMemoizationCounter += 6;
      }
      if (
        (board[1][2] == null &&
          board[1][0] === this.currentPlayer &&
          board[1][1] === this.currentPlayer) ||
        (board[1][2] == null &&
          board[0][2] === this.currentPlayer &&
          board[2][2] === this.currentPlayer)
      ) {
        return [1, 2];
      }

      // bottom left
      if (memType == "memoization") {
        this.memoizationCounter += 9;
      } else if (memType == "nonmemoization") {
        this.nonMemoizationCounter += 9;
      }
      if (
        (board[2][0] == null &&
          board[1][1] === this.currentPlayer &&
          board[0][2] === this.currentPlayer) ||
        (board[2][0] == null &&
          board[2][1] === this.currentPlayer &&
          board[2][2] === this.currentPlayer) ||
        (board[2][0] == null &&
          board[1][0] === this.currentPlayer &&
          board[0][0] === this.currentPlayer)
      ) {
        return [2, 0];
      }

      // bottom middle
      if (memType == "memoization") {
        this.memoizationCounter += 6;
      } else if (memType == "nonmemoization") {
        this.nonMemoizationCounter += 6;
      }
      if (
        (board[2][1] == null &&
          board[2][2] === this.currentPlayer &&
          board[2][0] === this.currentPlayer) ||
        (board[2][1] == null &&
          board[0][1] === this.currentPlayer &&
          board[1][1] === this.currentPlayer)
      ) {
        return [2, 1];
      }

      // bottom right
      if (memType == "memoization") {
        this.memoizationCounter += 9;
      } else if (memType == "nonmemoization") {
        this.nonMemoizationCounter += 9;
      }
      if (
        (board[2][2] == null &&
          board[0][2] === this.currentPlayer &&
          board[1][2] === this.currentPlayer) ||
        (board[2][2] == null &&
          board[0][0] === this.currentPlayer &&
          board[1][1] === this.currentPlayer) ||
        (board[2][2] == null &&
          board[2][0] === this.currentPlayer &&
          board[2][1] === this.currentPlayer)
      ) {
        return [2, 2];
      }
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
  font-size: 18pt;
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
p {
  width: 300px;
}
</style>
