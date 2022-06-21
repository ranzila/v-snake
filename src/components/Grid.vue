<template>
  <div class="grid">
    <Row
      v-for="index in this.rowsCnt"
      :key="index"
      :rowNb="index"
      :colsCnt="this.colsCnt"
      :snakeCoordinates="this.snakeCoordinates"
      :preyCoordinates="this.preyCoordinates"
    />
  </div>
</template>

<script>
import Row from "@/components/Row.vue";

export default {
  components: { Row },
  props: ["rowsCnt", "colsCnt", "snakeCoordinates", "preyCoordinates"],
};
</script>

<style>
.grid {
  width: 95%;
  padding: 7px;
  display: flex;
  flex-direction: column;
  align-items: center;
  align-content: center;
  justify-content: center;
}
</style>
