<template>
  <el-card class="console" :body-style="{ padding: '0px' }">
    <div class="option">
      <el-button
        v-if="pause"
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
      <span class="label" style="margin-right: 10px;">速度</span>
      <el-slider
        style="flex: auto"
        :value="state.speed"
        :min="0"
        :max="4"
        :show-tooltip="false"
        @input="(speed) => onUpdate({ speed })"
      />
    </div>
    <div class="option size-option">
      <el-input-number
        style="width: 50px;"
        :value="state.rows"
        :min="5"
        :max="30"
        size="small"
        :controls="false"
        @change="(rows) => onUpdate({ rows })"
      />
      <span class="label">行</span>
      <el-input-number
        style="width: 50px;"
        :value="state.cols"
        :min="5"
        :max="50"
        size="small"
        :controls="false"
        @change="(cols) => onUpdate({ cols })"
      />
      <span class="label">列</span>
    </div>
  </el-card>
</template>

<script>
export default {
  props: ["state", "pause"],
  methods: {
    onUpdate(val) {
      this.$emit("update:state", { ...this.state, ...val });
      val.speed === undefined && this.$emit("onReset");
    },
  },
};
</script>

<style scoped>
.console {
  padding: 10px 20px;
  position: fixed;
  top: 10px;
  left: calc(100% - 21px);
  transition: 0.5s;
}
.console:hover {
  transform: translateX(-190px);
  transition: 0.2s;
}
.option {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.el-button >>> i[class^="el-icon"] {
  font-size: 24px;
}
.label {
  font-size: 14px;
  color: #8492a6;
}
</style>
