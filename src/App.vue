<template lang="pug">
  #app
    .container
      .row(v-for="(row, iRow) in world")
        div(v-for="(cell, iCol) in row" :key="iCol" @click="toggle(iRow,iCol)")
          div.border.p-2(:class="{'bg-primary' : cell,'bg-secondary' : !cell}")
      .row
        .col-12
          b-button(variant="danger" @click="initWorld()") Reset
          b-button(variant="success" @click="tick()") Play
</template>

<script>
import cell from "./components/cell.vue";
export default {
  name: "App",
  components: {
    cell
  },
  data() {
    return {
      world: [],
      stopped: true
    };
  },
  created() {
    this.initWorld();
  },
  methods: {
    // initWorld(rowSize = 63, colSize = 63) {
    initWorld(rowSize = 20, colSize = 20) {
      this.world = [];
      for (let rows = 0; rows < rowSize; rows++) {
        const row = [];
        for (let cols = 0; cols < colSize; cols++) {
          row.push(false);
        }
        this.world.push(row);
      }
    },
    tick() {
      let tempWord = this.world.slice();
      for (var row = 0; row < this.world.length; row++) {
        for (var col = 0; col < this.world[0].length; col++) {
          tempWord[row][col] = this.aliveNextTick(row, col);
        }
      }
      this.world = tempWord.slice();
    },
    aliveNextTick(row, col) {
      let numNeighbours = this.getNumNeighbours(row, col);
      let alive = this.world[row][col];
      switch (alive) {
        case true:
          //Death by isolation: Each live cell with one or fewer live neighbors will die in the next generation.
          //Death by overcrowding: Each live cell with four or more live neighbors will die in the next generation.
          //Survival: Each live cell with either two or three live neighbors will remain alive for the next generation.
          return numNeighbours <= 1 || numNeighbours >= 4 ? false : true;
        case false:
          //Births: Each dead cell adjacent to exactly three live neighbors will become live in the next generation.
          return numNeighbours === 3 ? true : false;
      }
    },
    play(speed = 1) {
      this.stopped = false
      speed
    },
    toggle(row, col) {
      let tempWord = this.world.slice();
      tempWord[row][col] = !this.world[row][col];
      this.world = tempWord.slice();
      console.log("Cords " + row + "," + col);
      console.log("alive Next Tick: ", this.aliveNextTick(row, col));
      console.log("Num Neighbours", this.getNumNeighbours(row, col));
      console.log("-----------------------------------------------");
    },
    getNumNeighbours(cellRow, cellCol) {
      const startRow = cellRow - 1;
      const startCol = cellCol - 1;

      const endRow = cellRow + 1;
      const endCol = cellCol + 1;

      let numNeighbours = 0;

      for (var row = startRow; row <= endRow; row++) {
        if (row < 0 || row >= this.world.length) {
          continue;
        }
        for (var col = startCol; col <= endCol; col++) {
          if (col < 0 || col >= this.world[0].length) {
            continue;
          }
          if (row === cellRow && col === cellCol) {
            continue;
          }
          if (this.world[row][col]) {
            numNeighbours++;
          }
        }
      }
      return numNeighbours;
    }
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
