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
               [0, 0, 0, 0, 0, 0, 0, 0, 0]],
        // grid: [[1, 2, 3, 4, 5, 6, 7, 8, 9],
        //        [4, 5, 6, 7, 8, 9, 1, 2, 3],
        //        [7, 8, 9, 1, 2, 3, 4, 5, 6],
        //        [2, 3, 4, 5, 6, 7, 8, 9, 1],
        //        [5, 6, 7, 8, 9, 1, 2, 3, 4],
        //        [8, 9, 1, 2, 3, 4, 5, 6, 7],
        //        [3, 4, 5, 6, 7, 8, 9, 1, 2],
        //        [6, 7, 8, 9, 1, 2, 3, 4, 5],
        //        [9, 1, 2, 3, 4, 5, 6, 7, 8]],
        mutable: [[false, false, false, false, false, false, false, false, false],
                  [false, false, false, false, false, false, false, false, false],
                  [false, false, false, false, false, false, false, false, false],
                  [false, false, false, false, false, false, false, false, false],
                  [false, false, false, false, false, false, false, false, false],
                  [false, false, false, false, false, false, false, false, false],
                  [false, false, false, false, false, false, false, false, false],
                  [false, false, false, false, false, false, false, false, false],
                  [false, false, false, false, false, false, false, false, false]],
        currRow: 0,
        currCol: 0,
        numFilled: 1,
        boxIndices: [[0, 0], [0, 3], [0, 6], [3, 0], [3, 3], [3, 6], [6, 0], [6, 3], [6, 6]]
      }
    },
    methods: {
      // updates number in grid
      updateNum(event: KeyboardEvent) {
        if (event.key == 'a') this.generateBoard(30);
        if (event.key == 'b') console.log(this.solve(false));

        let val: number = +event.key;
        if (this.mutable[this.currRow][this.currCol] && !isNaN(val)) {
          if (event.key == ' ' || val == 0) {
            if (this.grid[this.currRow][this.currCol] != 0) this.numFilled--;
            this.grid[this.currRow][this.currCol] = 0;
          } else {
            if (this.grid[this.currRow][this.currCol] == 0) this.numFilled++;
            this.grid[this.currRow][this.currCol] = val;
          }
          if (this.numFilled == 81 && this.valid(this.grid)) console.log("You win!");
        }
      },

      valid(grid: Array<Array<number>>) {
        // check rows
        for (let row = 0; row < 9; row++) {
          let mask = 0;
          for (let col = 0; col < 9; col++) {
            let num = grid[row][col];
            if (num == 0) continue;
            if (mask >> num-1 & 1) return false;  
            mask += 1 << num-1;
          }
        }
        // check cols
        for (let col = 0; col < 9; col++) {
          let mask = 0;
          for (let row = 0; row < 9; row++) {
            let num = grid[row][col];
            if (num == 0) continue;
            if (mask >> num-1 & 1) return false;  
            mask += 1 << num-1;
          }
        }
        // check boxes
        for (let box of this.boxIndices) {
          let mask = 0;
          for (let i = 0; i < 9; i++) {
            let num = grid[box[0] + Math.floor(i/3)][box[1] + i % 3];
            if (num == 0) continue;
            if (mask >> num-1 & 1) return false;  
            mask += 1 << num-1;
          }
        }
        return true;
      },

      solve(inplace: boolean) {
        let tempGrid: Array<Array<number>>;
        if (inplace) tempGrid = this.grid;
        else tempGrid = JSON.parse(JSON.stringify(this.grid));
        let indices = Array();
        for (let row = 0; row < 9; row++) {
          for (let col = 0; col < 9; col++) {
            if (this.grid[row][col] == 0) {
              indices.push([row, col]);
            }
          }
        }

        let numSols = 0;
        for (let i = 0; i < indices.length; i++) {
          if (i < 0 || numSols > 1) return numSols;

          let row = indices[i][0];
          let col = indices[i][1];
          tempGrid[row][col]++;
          if (tempGrid[row][col] == 10) {
            tempGrid[row][col] = 0;
            i -= 2;
          }
          while (!this.valid(tempGrid)) {
            tempGrid[row][col]++;
            if (tempGrid[row][col] == 10) {
              tempGrid[row][col] = 0;
              i -= 2;
              break;
            }
          }
          if (i == indices.length-1) {
            numSols++;
            i--;
          }
        }
      },

      generateBoard(difficulty: number) {
        this.numFilled = 81 - difficulty;
        this.solve(true);
        let indices = Array();
        for (let row = 0; row < 9; row++) {
          for (let col = 0; col < 9; col++) {
            indices.push([row, col]);
          }
        }
        indices = indices.sort((a, b) => 0.5 - Math.random());
        let i = 0;
        let last = 0;
        while (difficulty > 0) {
          last = this.grid[indices[i][0]][indices[i][1]];
          this.grid[indices[i][0]][indices[i][1]] = 0;
          this.mutable[indices[i][0]][indices[i][1]] = true;
          i++;
          difficulty--;
        }
        while (this.solve(false) != 1) {
          this.grid[indices[i-1][0]][indices[i-1][1]] = last;
          this.mutable[indices[i-1][0]][indices[i-1][1]] = false;
          last = this.grid[indices[i][0]][indices[i][1]];
          this.grid[indices[i][0]][indices[i][1]] = 0;
          this.mutable[indices[i][0]][indices[i][1]] = true;
          i++;
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