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
      <span class="label" style="margin-right: 12px;">速度</span>
      <el-slider
        style="flex: auto"
        :value="state.speed"
        :min="0"
        :max="4"
        :show-tooltip="false"
        @input="(speed) => onUpdate('speed', speed)"
      />
    </div>
    <div class="option">
      <el-input-number
        style="width: 50px;"
        :value="state.rows"
        :disabled="!pause"
        :min="5"
        :max="30"
        size="small"
        :controls="false"
        @change="(rows) => onUpdate('rows', rows)"
      />
      <span class="label">行</span>
      <el-input-number
        style="width: 50px;"
        :disabled="!pause"
        :value="state.cols"
        :min="5"
        :max="50"
        size="small"
        :controls="false"
        @change="(cols) => onUpdate('cols', cols)"
      />
      <span class="label">列</span>
    </div>
    <div class="option">
      <el-slider
        style="flex: auto"
        :disabled="!pause"
        v-model="percent"
        :format-tooltip="formatTooltip"
      />
      <el-button
        class="refresh"
        :disabled="!pause"
        circle
        icon="el-icon-refresh"
        @click="$emit('onRandom', percent)"
      />
    </div>
  </el-card>
</template>

<script>
export default {
  props: ["state", "pause"],
  data() {
    return {
      percent: 20,
    };
  },
  methods: {
    onUpdate(name, val) {
      let updated = { ...this.state };
      name != "speed" && (val = val || 5);
      updated[name] = val;
      this.$emit("update:state", updated);
      name != "speed" && this.$emit("onReset");
    },
    formatTooltip(val) {
      return val + "%";
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
.el-button >>> i {
  font-size: 24px;
}
.label {
  font-size: 14px;
  color: #8492a6;
}
.el-button.refresh {
  margin-left: 12px;
  padding: 8px;
}
.el-button.refresh >>> i {
  font-size: 14px;
}
</style>
