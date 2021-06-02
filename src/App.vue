<template>
  <div id="app">
    <div class="score">{{ "live: " + count }}</div>
    <Board :board="board" @click="onClickBoard" />
    <Console
      @onStart="onStart"
      @onPause="onPause"
      @onNext="onNext"
      @onReset="onReset"
      @onRandom="onRandom"
      :pause="pause"
      :state.sync="state"
    />
    <Preset @onPreset="onPreset" />
    <div class="version">ver 1.0</div>
  </div>
</template>

<script>
import Board from "@/components/Board";
import Console from "@/components/Console";
import Preset from "@/components/Preset";

export default {
  components: { Board, Console, Preset },
  data() {
    return {
      board: [],
      count: 0,
      pause: true,
      state: {
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
      let rows = this.state.rows;
      let cols = this.state.cols;
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
      this.count = count;
      console.log("live: " + count);
    },
    evolve() {
      if (this.pause) {
        return;
      }
      this.toNextTick();
      if (this.count === 0) {
        this.onReset();
      } else {
        setTimeout(this.evolve, this.interval);
      }
    },
    onClickBoard(e) {
      let coordinate = e.target.cID;
      if (!coordinate) return;
      let y = coordinate.split("/")[0];
      let x = coordinate.split("/")[1];
      this.board[y][x] = !this.board[y][x];
      this.board[y][x] ? this.count++ : this.count--;
      this.board = [...this.board];
    },
    onStart() {
      console.log("start");
      this.pause = false;
      this.evolve();
    },
    onPause() {
      console.log("pause");
      this.pause = true;
    },
    onNext() {
      console.log("next");
      this.pause = true;
      this.toNextTick();
    },
    onReset() {
      console.log("reset");
      this.pause = true;
      this.count = 0;
      this.board = [...Array(this.state.rows)].map(() =>
        Array(this.state.cols).fill(false)
      );
    },
    onRandom(percent) {
      console.log("random");
      this.onReset();
      let x, y;
      let sum = this.state.rows * this.state.cols;
      let subset = [];
      for (let i = 0; i < sum; i++) {
        subset.push(i);
      }
      subset.sort(() => Math.random() - 0.5);
      this.count = Math.round((sum * percent) / 100);
      subset = subset.slice(0, this.count);
      subset.forEach((val) => {
        y = Math.floor(val / this.state.cols);
        x = val % this.state.cols;
        this.board[y][x] = true;
      });
    },
    onPreset(board) {
      console.log("preset");
      let rows = board.length;
      let cols = board[0].length;
      this.state.rows = rows;
      this.state.cols = cols;
      this.onReset();
      for (let y = 0; y < rows; y++) {
        for (let x = 0; x < cols; x++) {
          board[y][x] && ++this.count && (this.board[y][x] = true);
        }
      }
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
  font-size: 24px;
}
.version {
  position: fixed;
  bottom: 0;
  right: 0;
  color: #909399;
}
</style>
