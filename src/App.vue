<template>
  <div id="app">
    <div class="score">{{ "live: " + state.count }}</div>
    <Board :board="board" @click="onClickBoard" />
    <Console
      @onStart="onStart"
      @onPause="onPause"
      @onNext="onNext"
      @onReset="onReset"
      :state.sync="state"
    />
  </div>
</template>

<script>
import Board from "@/components/Board";
import Console from "@/components/Console";

export default {
  components: { Board, Console },
  data() {
    return {
      board: [],
      state: {
        count: 0,
        pause: true,
        rows: 15,
        cols: 15,
        speed: 2,
      },
    };
  },
  computed: {
    interval() {
      switch (this.state.speed) {
        case 0:
          return 1000;
        case 1:
          return 750;
        case 2:
          return 500;
        case 3:
          return 350;
        case 4:
          return 100;
        default:
          return 500;
      }
    },
  },
  created() {
    this.onReset();
  },
  methods: {
    toNextTick() {
      let count = 0;
      let rows = this.board.length;
      let cols = this.board[0].length;
      let next = [];
      for (const row of this.board) {
        next.push([...row]);
      }
      for (let row = 0; row < rows; row++) {
        for (let col = 0; col < cols; col++) {
          let liveNeighbors = 0;
          for (let i = -1; i < 2; i++) {
            for (let j = -1; j < 2; j++) {
              if (!(i === 0 && j === 0)) {
                let y = row + i;
                let x = col + j;
                if (
                  x >= 0 &&
                  x < cols &&
                  y >= 0 &&
                  y < rows &&
                  this.board[y][x] === true
                ) {
                  liveNeighbors++;
                }
              }
            }
          }
          if (
            this.board[row][col] === true &&
            (liveNeighbors < 2 || liveNeighbors > 3)
          ) {
            next[row][col] = false;
          }
          if (this.board[row][col] === false && liveNeighbors == 3) {
            next[row][col] = true;
          }
          next[row][col] && count++;
        }
      }
      this.board = next;
      this.state.count = count;
      console.log("live: " + count);
    },
    evolve() {
      if (this.state.pause) {
        return;
      }
      this.toNextTick();
      if (this.state.count === 0) {
        this.onReset();
      } else {
        setTimeout(this.evolve, this.interval);
      }
    },
    onClickBoard(e) {
      let y = e.target.cID.split("/")[0];
      let x = e.target.cID.split("/")[1];
      let nextBoard = [];
      for (const row of this.board) {
        nextBoard.push([...row]);
      }
      nextBoard[y][x] = !nextBoard[y][x];
      nextBoard[y][x] ? this.state.count++ : this.state.count--;
      this.board = nextBoard;
    },
    onStart() {
      console.log("start");
      this.state.pause = false;
      this.evolve();
    },
    onPause() {
      console.log("pause");
      this.state.pause = true;
    },
    onNext() {
      console.log("next");
      this.state.pause = true;
      this.toNextTick();
    },
    onReset() {
      console.log("reset");
      this.state.pause = true;
      this.state.count = 0;
      this.board = Array.from(Array(this.state.rows)).map(() =>
        Array(this.state.cols).fill(false)
      );
    },
  },
};
</script>

<style scoped>
#app {
  height: 100%;
  display: grid;
  place-content: center;
}
.score {
  position: fixed;
}
</style>
