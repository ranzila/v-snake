<template>
  <div class="row">
    <Cell
      v-for="col in this.colsCnt"
      :key="col"
      :x="col"
      :y="this.rowNb"
      :isSnake="this.isSnakeBody(col, this.rowNb)"
      :isPrey="this.isPrey(col, this.rowNb)"
    />
  </div>
</template>

<script>
import _, { isEqual, contains } from "underscore";
import Cell from "@/components/Cell.vue";

export default {
  components: { Cell },
  name: "row",
  props: ["colsCnt", "rowNb", "snakeCoordinates", "preyCoordinates"],

  methods: {
    isSnakeBody(x, y) {
      return this.snakeCoordinates.some(c => c.x == x && c.y == y);
    },
    isPrey(x, y) {
      return _.isEqual(this.preyCoordinates, { x, y });
    },
  },
};
</script>

<style>
.row {
  display: flex;
  flex-direction: row;
  justify-content: center;
  flex-wrap: nowrap;
}
</style>
