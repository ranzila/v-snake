/* eslint-disable prettier/prettier */ /* eslint-disable prettier/prettier */
<template>
  <div class="container">
    <h1>V-SNAKE</h1>
    <div class="controls">
      <button @click="this.handleStartBtn()" class="ctrlBtn">
        {{ this.paused ? "\▶" : "| |" }}
      </button>
      <button @click="this.reset()" class="ctrlBtn">
        {{ "\↺" }}
      </button>
    </div>
    <div class="game-infos">
      <span>
        score: <b>{{ this.score }}</b>
        &nbsp;
      </span>
      <span>
        level: <b>{{ this.level }}</b>
      </span>
      <span>
        <button class="levelBtn">
          {{ "\u25b2" }}
        </button>
      </span>
      <span>
        <button class="levelBtn">
          {{ "\u25bc" }}
        </button>
      </span>
    </div>
    <Grid
      :rowsCnt="this.rowsCnt"
      :colsCnt="this.colsCnt"
      :snakeCoordinates="this.snakeCoordinates"
      :preyCoordinates="this.preyCoordinates"
    />
  </div>
</template>

<script>
/* eslint-disable */
import Grid from "@/components/Grid.vue";
import _, { isEqual } from "underscore";

const Direction = {
  UP: "Up",
  DOWN: "Down",
  LEFT: "Left",
  RIGHT: "Right",
};

export default {
  components: { Grid },
  name: "App",

  data() {
    return {
      score: null,
      level: null,
      rowsCnt: 30,
      colsCnt: 30,
      direction: "",
      snakeCoordinates: [],
      snakeSize: 0,
      preyCoordinates: [],
      paused: true,
      movementInterval: null,
    };
  },
  created() {
    this.init();
  },
  methods: {
    init() {
      let snakeCoordinates = [];
      this.score = 0;
      this.level = 1;
      this.snakeSize = 3;
      for (let i = this.snakeSize; i > 0; i--) {
        snakeCoordinates.push({ x: i, y: 1 });
      }
      console.log("INITIAL SNAKE POSITION");
      this.logObj(snakeCoordinates);
      console.trace(JSON.stringify(this.snake));
      this.direction = Direction.RIGHT;
      this.snakeCoordinates = snakeCoordinates.slice();
      this.setPreyCoordinates();
      this.setupKeysEvents();
    },
    start() {
      this.paused = false;
      this.movementInterval = setInterval(this.move, 1000 / this.level);
    },
    pause() {
      this.paused = true;
      clearInterval(this.movementInterval);
    },
    togglePause() {
      this.paused ? this.start() : this.pause();
    },
    reset() {
      this.pause();
      this.init();
    },
    handleStartBtn() {
      if (this.paused) this.start();
      else this.pause();
    },
    setupKeysEvents() {
      window.addEventListener("keydown", event => {
        console.log(`Key pressed: ${event.code}`);
        switch (event.code) {
          case "ArrowUp":
            this.direction = Direction.UP;
            break;
          case "ArrowDown":
            this.direction = Direction.DOWN;
            break;
          case "ArrowLeft":
            this.direction = Direction.LEFT;
            break;
          case "ArrowRight":
            this.direction = Direction.RIGHT;
            break;
          case "Space":
            this.togglePause();
            break;
        }
      });
    },
    copyObj(obj) {
      let copy = {};
      for (const key in obj) {
        copy[key] = obj[key];
      }

      return copy;
    },
    logObj(obj) {
      console.log(`> LOG: ${JSON.stringify(obj)}`);
    },
    move() {
      if (!this.paused) {
        console.log("BEFORE MOVE");
        this.logObj(this.snakeCoordinates);
        this.logObj(this.getHead());
        let updatedCoordinates = this.snakeCoordinates.slice();
        let prevCoord = this.copyObj(this.getHead());

        console.log("NEW MOVE");
        console.log(`Head of the snake is ${JSON.stringify(this.getHead())}`);

        updatedCoordinates.forEach((c, i) => {
          console.log(
            `At IDX ${updatedCoordinates.findIndex(coord =>
              _.isEqual(c, coord)
            )}`
          );
          console.log(
            `${JSON.stringify(c)} is ${this.isHead(c) ? "head" : "body"}`
          );
          if (this.isHead(c)) {
            if (this.validateMove(c)) {
              if (this.direction == Direction.DOWN) c.y += 1;
              if (this.direction == Direction.UP) c.y -= 1;
              if (this.direction == Direction.RIGHT) c.x += 1;
              if (this.direction == Direction.LEFT) c.x -= 1;
            } else {
              console.error("Invalid Snake Position");
              this.pause();
            }
          } else {
            let tmpC = this.copyObj(c);
            updatedCoordinates[i] = this.copyObj(prevCoord);
            prevCoord = tmpC;
          }
        });

        console.log("AFTER MOVE");
        this.logObj(updatedCoordinates);

        this.snakeCoordinates = updatedCoordinates;

        if (
          updatedCoordinates[0].x == this.preyCoordinates.x &&
          updatedCoordinates[0].y == this.preyCoordinates.y
        ) {
          this.score++;
          // TODO: Take into account rows & cols size
          this.snakeSize++;
          this.setPreyCoordinates();
        }
      }
    },
    validateMove(c) {
      return (
        (c.x + 1 <= this.colsCnt && this.direction == Direction.RIGHT) ||
        (c.x - 1 >= 1 && this.direction == Direction.LEFT) ||
        (c.y + 1 <= this.rowsCnt && this.direction == Direction.DOWN) ||
        (c.y - 1 >= 1 && this.direction == Direction.UP)
      );
    },
    getPreviousCoordinates(c) {
      let snakeCoord = this.snakeCoordinates.concat();
      let actualIndex = snakeCoord.findIndex(
        coord => coord.x == c.x && coord.y == c.y
      );

      let prevCoord = this.copyObj(
        snakeCoord[actualIndex > 0 ? actualIndex - 1 : 0]
      );

      return { x: prevCoord.x, y: prevCoord.y };
    },
    getHead() {
      return this.snakeCoordinates[0];
    },
    getTail() {
      return this.snakeCoordinates[this.snake.size - 1];
    },
    isHead(coord) {
      return _.isEqual(this.snakeCoordinates[0], coord);
    },
    isTail(coord) {
      return _.isEqual(this.snake.coordinates[this.snake.size - 1], coord);
    },
    setPreyCoordinates() {
      do {
        this.preyCoordinates = {
          x: Math.floor(Math.random() * this.colsCnt),
          y: Math.floor(Math.random() * this.rowsCnt),
        };
      } while (this.snakeCoordinates.includes(this.preyCoordinates));
    },
  },
};
</script>

<style>
body {
  background-color: darkslategray;
}

.container {
  margin: auto;
  padding: 10px;
  width: 75%;
  height: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-content: center;
  align-items: center;
  opacity: 85%;
}

h1 {
  text-align: center;
  color: lightgreen;
  font-family: Courier;
  margin-bottom: 7px;
}

.game-infos {
  display: flex;
  flex-direction: row;
  justify-content: center;
  flex-wrap: nowrap;
  width: 50%;
  font-family: Courier;
  font-size: 33px;
  color: yellow;
}

.controls {
  display: flex;
  flex-direction: row;
  justify-content: center;
}

.ctrlBtn {
  color: lightgreen;
  text-align: center;
  vertical-align: middle;
  margin-bottom: 7px;
  background-color: darkslategray;
  font-size: 55px;
  border: none;
  cursor: pointer;
}

.ctrlBtn:hover {
  color: white;
}

.levelBtn {
  border: none;
  font-size: 33px;
  color: yellow;
  background-color: transparent;
}
</style>
