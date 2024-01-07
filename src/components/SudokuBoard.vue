<!-- SudokuBoard.vue -->

<script lang="ts">
  import SudokuCell from './SudokuCell.vue';

  export default {
    name: 'SudokuBoard',
    components: {
      SudokuCell
    },
    // Add data, methods, or other component options here
    data() {
      return {
        grid: [[0, 0, 0, 0, 0, 0, 0, 0, 0],
               [0, 0, 0, 0, 0, 0, 0, 0, 0],
               [0, 0, 0, 0, 0, 0, 0, 0, 0],
               [0, 0, 0, 0, 0, 0, 0, 0, 0],
               [0, 0, 0, 0, 0, 0, 0, 0, 0],
               [0, 0, 0, 0, 0, 0, 0, 0, 0],
               [0, 0, 0, 0, 0, 0, 0, 0, 0],
               [0, 0, 0, 0, 0, 0, 0, 0, 0],
               [0, 0, 0, 0, 0, 0, 0, 0, 3]],
        // grid: [[1, 2, 3, 4, 5, 6, 7, 8, 9],
        //        [4, 5, 6, 7, 8, 9, 1, 2, 3],
        //        [7, 8, 9, 1, 2, 3, 4, 5, 6],
        //        [2, 3, 4, 5, 6, 7, 8, 9, 1],
        //        [5, 6, 7, 8, 9, 1, 2, 3, 4],
        //        [8, 9, 1, 2, 3, 4, 5, 6, 7],
        //        [3, 4, 5, 6, 7, 8, 9, 1, 2],
        //        [6, 7, 8, 9, 1, 2, 3, 4, 5],
        //        [9, 1, 2, 3, 4, 5, 6, 7, 8]],
        mutable: [[true, true, true, true, true, true, true, true, true],
                  [true, true, true, true, true, true, true, true, true],
                  [true, true, true, true, true, true, true, true, true],
                  [true, true, true, true, true, true, true, true, true],
                  [true, true, true, true, true, true, true, true, true],
                  [true, true, true, true, true, true, true, true, true],
                  [true, true, true, true, true, true, true, true, true],
                  [true, true, true, true, true, true, true, true, true],
                  [true, true, true, true, true, true, true, true, false]],
        currRow: 0,
        currCol: 0,
        numFilled: 1,
        boxIndices: [[0, 0], [0, 3], [0, 6], [3, 0], [3, 3], [3, 6], [6, 0], [6, 3], [6, 6]]
      }
    },
    methods: {
      // updates number in grid
      updateNum(event: KeyboardEvent) {
        if (event.key == 'a') this.solve();

        let val: number = +event.key;
        if (this.mutable[this.currRow][this.currCol] && !isNaN(val)) {
          if (event.key == ' ' || val == 0) {
            if (this.grid[this.currRow][this.currCol] != 0) this.numFilled--;
            this.grid[this.currRow][this.currCol] = 0;
          } else {
            if (this.grid[this.currRow][this.currCol] == 0) this.numFilled++;
            this.grid[this.currRow][this.currCol] = val;
          }
          if (this.numFilled == 81 && this.valid()) console.log("You win!");
        }
      },

      valid() {
        // check rows
        for (let row = 0; row < 9; row++) {
          let mask = 0;
          for (let col = 0; col < 9; col++) {
            let num = this.grid[row][col];
            if (num == 0) continue;
            if (mask >> num-1 & 1) return false;  
            mask += 1 << num-1;
          }
        }
        // check cols
        for (let col = 0; col < 9; col++) {
          let mask = 0;
          for (let row = 0; row < 9; row++) {
            let num = this.grid[row][col];
            if (num == 0) continue;
            if (mask >> num-1 & 1) return false;  
            mask += 1 << num-1;
          }
        }
        // check boxes
        for (let box of this.boxIndices) {
          let mask = 0;
          for (let i = 0; i < 9; i++) {
            let num = this.grid[box[0] + Math.floor(i/3)][box[1] + i % 3];
            if (num == 0) continue;
            if (mask >> num-1 & 1) return false;  
            mask += 1 << num-1;
          }
        }
        return true;
      },

      solve() {
        // let tempGrid = Array(9);
        let tempGrid = this.grid;
        let indices = Array();
        for (let row = 0; row < 9; row++) {
          // tempGrid[row] = Array(9);
          for (let col = 0; col < 9; col++) {
            // tempGrid[row][col] = this.grid[row][col];
            if (this.grid[row][col] == 0) {
              indices.push([row, col]);
            }
          }
        }

        for (let i = 0; i < indices.length; i++) {
          if (i < 0) {
            console.log("No solution");
            break;
          }
          console.log(JSON.stringify(tempGrid));

          let row = indices[i][0];
          let col = indices[i][1];
          tempGrid[row][col]++;
          if (tempGrid[row][col] == 10) {
            tempGrid[row][col] = 0;
            i -= 2;
          }
          while (!this.valid()) {
            tempGrid[row][col]++;
            if (tempGrid[row][col] == 10) {
              tempGrid[row][col] = 0;
              i -= 2;
            }
          }
        }
      }
    }
  }
</script>

<template>
  <div class="sudoku-board" @keydown="updateNum($event)" tabindex="-1">
    <div v-for="row in 9" class="sudoku-row">
      <SudokuCell v-for="col in 9" :row="row-1" :col="col-1" :val="grid[row-1][col-1]"
      :class="{ active: currRow == row-1 && currCol == col-1, immutable: !mutable[row-1][col-1]}"
      @click="currRow = row-1; currCol = col-1;"/>
    </div>
  </div>
</template>

<style scoped>
div:focus {
  outline: 0px;
}

.sudoku-row {
  /* border: 1px solid red; */
  display: flex;
}
</style>