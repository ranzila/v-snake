<template>
  <div @keyupclass="container">
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
      <span
        >score: <b>{{ this.score }}</b> &nbsp;&nbsp;</span
      >
      <span
        >level: <b>{{ this.level }}</b></span
      >
    </div>
    <Grid
      :rowsCnt="this.rowsCnt"
      :colsCnt="this.colsCnt"
      :snakeCoordinates="this.snake.coordinates"
      :preyCoordinates="this.prey.coordinates"
    />
  </div>
</template>

<script>
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
      score: 0,
      level: 1,
      rowsCnt: 30,
      colsCnt: 30,
      direction: "",
      snake: {
        size: 0,
        coordinates: [],
      },
      prey: {
        coordinates: {},
      },
      paused: true,
      movementInterval: null,
    };
  },
  created() {
    this.init();
  },
  methods: {
    init() {
      let updatedSnake = {};
      updatedSnake = Object.assign(updatedSnake, this.snake);
      updatedSnake.size = 3;
      updatedSnake.coordinates = [];

      for (var i = 1; i <= updatedSnake.size; i++) {
        updatedSnake.coordinates.push({ x: i, y: 1 });
      }
      this.snake = updatedSnake;
      this.direction = Direction.RIGHT;
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
            this.pause();
            break;
        }
      });
    },
    move() {
      if (!this.paused) {
        if (
          this.direction == Direction.LEFT ||
          this.direction == Direction.RIGHT
        )
          this.moveX();
        else this.moveY();

        if (_.isEqual(this.snake.coordinates, this.prey.coordinates)) {
          this.score++;
          // TODO: Take into account rows & cols size
          this.snake.size++;
        }
      }
    },
    // To Refactor, not DRY !
    moveX() {
      let coord = this.snake.coordinates;
      const head = coord[0];
      const tail = coord[this.size - 1];

      if (this.direction == Direction.RIGHT) {
        coord.forEach(c => {
          if (c.x + 1 <= this.rowsCnt && (this.isHead(c) || c.y == head.y))
            c.x += 1;
          else if (c.y < head.y) c.y += 1;
          else if (c.y > head.y) c.y -= 1;
        });
      } else if (this.direction == Direction.LEFT) {
        coord.forEach(c => {
          if (c.x - 1 >= 0 && (this.isHead(c) || c.y == head.y)) c.x -= 1;
          else if (c.y < head.y) c.y += 1;
          else if (c.y > head.y) c.y -= 1;
        });
      }

      this.snake.coordinates = coord;
    },
    moveY() {
      let coord = this.snake.coordinates;
      const head = coord[0];
      const tail = coord[this.size - 1];

      if (this.direction == Direction.DOWN) {
        coord.forEach(c => {
          if (c.y + 1 <= this.colsCnt && (this.isHead(c) || c.x == head.x))
            c.y += 1;
          else if (c.x < head.x) c.x += 1;
          else if (c.x > head.x) c.x -= 1;
        });
      } else if (this.direction == Direction.UP) {
        coord.forEach(c => {
          if (c.y - 1 >= 0 && (this.isHead(c) || c.x == head.x)) c.y -= 1;
          else if (c.x < head.x) c.x += 1;
          else if (c.x > head.x) c.x -= 1;
        });
      }

      this.snake.coordinates = coord;
    },
    getHead() {
      return this.snake.coordinates[0];
    },
    getTail() {
      return this.snake.coordinates[this.snake.size - 1];
    },
    isHead(coord) {
      _.isEqual(this.snake.coordinates[0], coord);
    },
    isTail(coord) {
      const coordLength = this.snake.coordinates.length;
      return _.isEqual(this.snake.coordinates[coordLength - 1], coord);
    },
    setPreyCoordinates() {
      do {
        this.prey.coordinates = {
          x: Math.floor(Math.random() * this.colsCnt),
          y: Math.floor(Math.random() * this.rowsCnt),
        };
      } while (this.snake.coordinates.includes(this.prey.coordinates));
    },
  },
  watch: {
    snake(newSnake, oldSnake) {
      if (newSnake.size > oldSnake.size) {
        let newX = 0;
        let newY = 0;

        if (
          this.direction == Direction.RIGTH &&
          this.getTail().y == this.getHead().y
        ) {
          newX = this.getTail().x - 1;
          newY = this.getTail().y;
        }
        let extension = { x: newX, y: newY };

        this.snake.coordinates.push(extension);
      }
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
  justify-content: space-between;
  align-content: center;
  font-family: Courier;
  font-size: 11px;
  color: yellow;
}

.controls {
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: center;
}

.ctrlBtn {
  color: lightgreen;
  text-align: center;
  vertical-align: middle;
  margin-bottom: 7px;
  background-color: darkslategray;
  font-size: 33px;
  border: none;
  cursor: pointer;
}

.ctrlBtn:hover {
  color: white;
}
</style>
