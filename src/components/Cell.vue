<template>
  <div
    class="cell"
    :class="{
      snake: this.isSnake,
      prey: this.isPrey,
    }"
  />
</template>

<script>
export default {
  name: "Cell",
  props: ["isPrey", "isSnake", "x", "y"],

  created() {
    if (this.isSnake || this.isPrey)
      console.log("ACTIVE CELL: " + this.x + " " + this.y);
  },
};
</script>

<style>
.cell {
  width: 5px;
  height: 5px;
  font-size: 7px;
  background-color: yellowgreen;
  border-color: lightgreen;
  border: 0.5px solid lightgreen;
}

.snake {
  background-color: #d5ffc4;
}

.prey {
  background-color: red;
}
</style>
