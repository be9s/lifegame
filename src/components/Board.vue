<template>
  <div class="board" @click="$emit('click', $event)">
    <div class="row" v-for="(row, y) in board" :key="y">
      <Cell v-for="(col, x) in row" :key="x" :live="col" :cID="y + '/' + x" />
    </div>
  </div>
</template>

<script>
import Cell from "@/components/Cell";
export default {
  components: { Cell },
  props: ["board"],
};
</script>

<style scoped>
.board {
  border: solid;
}
.row {
  display: flex;
  position: relative;
}
</style>
