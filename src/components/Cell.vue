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
};
</script>

<style>
.cell {
  width: 17px;
  height: 17px;
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
