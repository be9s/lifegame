<template>
  <div :class="['cell', { live: live }]" :cID.prop="cID"></div>
</template>
<script>
export default {
  props: ["live", "cID"],
};
</script>
<style scoped>
.cell {
  border: 1px solid #dcdfe6;
  width: 20px;
  height: 20px;
}
.cell:hover {
  background-color: #909399;
}
.live {
  background-color: #303133;
}
.live.cell:hover {
  background-color: #606266;
}
</style>
