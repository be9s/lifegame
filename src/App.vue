<template>
  <div id="app">
    <div class="score">{{ "live: " + count }}</div>
    <Board :board="board" @click="onClickBoard" />
    <div class="options">
      <div class="option">
        <el-button
          v-if="pause"
          circle
          type="primary"
          icon="el-icon-video-play"
          @click="onStart"
        />
        <el-button
          v-else
          circle
          type="warning"
          icon="el-icon-video-pause"
          @click="onPause"
        />
        <el-button
          circle
          type="success"
          icon="el-icon-d-arrow-right"
          @click="onNext"
        />
        <el-button
          circle
          type="danger"
          icon="el-icon-refresh-left"
          @click="onReset"
        />
      </div>
      <div class="option">
        <span class="label">速度</span>
        <el-slider
          style="flex: auto"
          v-model="speed"
          :min="0"
          :max="4"
          :show-tooltip="false"
        />
      </div>
      <div class="option">
        <span class="label">行数</span>
        <el-input-number
          style="flex: auto"
          size="small"
          controls-position="right"
          :min="5"
          :max="30"
          v-model="rows"
          @change="onReset"
        />
      </div>
      <div class="option">
        <span class="label">列数</span>
        <el-input-number
          style="flex: auto"
          size="small"
          controls-position="right"
          :min="5"
          :max="50"
          v-model="cols"
          @change="onReset"
        />
      </div>
    </div>
  </div>
</template>

<script>
import Board from "@/Board";

export default {
  components: { Board },
  data() {
    return {
      board: [],
      pause: true,
      count: 0,
      rows: 20,
      cols: 20,
      speed: 2,
    };
  },
  computed: {
    interval() {
      switch (this.speed) {
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
      let y = e.target.cID.split("/")[0];
      let x = e.target.cID.split("/")[1];
      let nextBoard = [];
      for (const row of this.board) {
        nextBoard.push([...row]);
      }
      nextBoard[y][x] = !nextBoard[y][x];
      nextBoard[y][x] ? this.count++ : this.count--;
      this.board = nextBoard;
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
      this.board = Array.from(Array(this.rows)).map(() =>
        Array(this.cols).fill(false)
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
.options {
  padding: 10px 20px;
  position: fixed;
  top: 0;
  right: 0;
}
.option {
  display: flex;
  justify-content: space-evenly;
  align-items: center;
}
.el-button >>> i[class^="el-icon"] {
  font-size: 24px;
}
.label {
  margin-right: 10px;
  font-size: 14px;
  color: #8492a6;
}
</style>
