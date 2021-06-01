<template>
  <div class="console">
    <div class="option">
      <el-button
        v-if="state.pause"
        circle
        type="success"
        icon="el-icon-video-play"
        @click="$emit('onStart')"
      />
      <el-button
        v-else
        circle
        type="warning"
        icon="el-icon-video-pause"
        @click="$emit('onPause')"
      />
      <el-button
        circle
        type="primary"
        icon="el-icon-d-arrow-right"
        @click="$emit('onNext')"
      />
      <el-button
        circle
        type="danger"
        icon="el-icon-refresh-left"
        @click="$emit('onReset')"
      />
    </div>
    <div class="option">
      <span class="label">速度</span>
      <el-slider
        style="flex: auto"
        :value="state.speed"
        :min="0"
        :max="4"
        :show-tooltip="false"
        @input="(speed) => $emit('update:state', { ...state, speed })"
      />
    </div>
    <div class="option">
      <span class="label">行数</span>
      <el-input-number
        style="flex: auto"
        :value="state.rows"
        :min="5"
        :max="30"
        size="small"
        controls-position="right"
        @change="
          (rows) => {
            $emit('update:state', { ...state, rows });
            $emit('onReset');
          }
        "
      />
    </div>
    <div class="option">
      <span class="label">列数</span>
      <el-input-number
        style="flex: auto"
        :value="state.cols"
        :min="5"
        :max="50"
        size="small"
        controls-position="right"
        @change="
          (cols) => {
            $emit('update:state', { ...state, cols });
            $emit('onReset');
          }
        "
      />
    </div>
  </div>
</template>

<script>
export default {
  props: ["state"],
};
</script>

<style scoped>
.console {
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
