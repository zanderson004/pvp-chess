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
        // mutable: [[false, false, false, false, false, false, false, false, false],
        //           [false, false, false, false, false, false, false, false, false],
        //           [false, false, false, false, false, false, false, false, false],
        //           [false, false, false, false, false, false, false, false, false],
        //           [false, false, false, false, false, false, false, false, false],
        //           [false, false, false, false, false, false, false, false, false],
        //           [false, false, false, false, false, false, false, false, false],
        //           [false, false, false, false, false, false, false, false, false],
        //           [false, false, false, false, false, false, false, false, false]],
        mutable: [[true, true, true, true, true, true, true, true, true],
                  [true, true, true, true, true, true, true, true, true],
                  [true, true, true, true, true, true, true, true, true],
                  [true, true, true, true, true, true, true, true, true],
                  [true, true, true, true, true, true, true, true, true],
                  [true, true, true, true, true, true, true, true, true],
                  [true, true, true, true, true, true, true, true, true],
                  [true, true, true, true, true, true, true, true, true],
                  [true, true, true, true, true, true, true, true, true],],
        currRow: 0,
        currCol: 0,
        numFilled: 1,
        boxIndices: [[0, 0], [0, 3], [0, 6], [3, 0], [3, 3], [3, 6], [6, 0], [6, 3], [6, 6]],
        won: false
      }
    },
    methods: {
      // updates number in grid
      updateNum(event: KeyboardEvent) {
        // if (event.key == 'a') this.generateBoard(2);
        // if (event.key == 'b') console.log(this.solve(true));

        let val: number = +event.key;
        if (this.mutable[this.currRow][this.currCol] && !isNaN(val)) {
          if (event.key == ' ' || val == 0) {
            if (this.grid[this.currRow][this.currCol] != 0) this.numFilled--;
            this.grid[this.currRow][this.currCol] = 0;
          } else {
            if (this.grid[this.currRow][this.currCol] == 0) this.numFilled++;
            this.grid[this.currRow][this.currCol] = val;
          }
          if (this.numFilled == 81 && this.valid(this.grid)) this.won = true; else this.won = false;
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

        // random array of all indices that need solving
        let indices = Array();
        for (let row = 0; row < 9; row++) {
          for (let col = 0; col < 9; col++) {
            if (this.grid[row][col] == 0) {
              indices.push([row, col]);
            }
          }
        }
        // indices = indices.sort((a, b) => 0.5 - Math.random());

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

        return numSols;
      },

      // seedBoard() {
      //   // random array of all indices
      //   let indices = Array();
      //   for (let row = 0; row < 9; row++) {
      //     for (let col = 0; col < 9; col++) {
      //       indices.push([row, col]);
      //     }
      //   }
      //   indices = indices.sort((a, b) => 0.5 - Math.random());

      //   for (let i = 0, target = 15; i < target; i++) {
      //     // random array of values
      //     let vals = Array();
      //     for (let val = 0; val < 9; val++) vals.push(val);
      //     vals = vals.sort((a, b) => 0.5 - Math.random());
      //     let j = 1;

      //     this.grid[indices[i][0]][indices[i][1]] = vals[0];
      //     while (this.valid(this.grid) == false) {
      //       this.grid[indices[i][0]][indices[i][1]] = vals[j];
      //       j++;
      //       if (j == 10) {
      //         target++;
      //         break;
      //       }
      //     }

      //     if (target = 82) break;
      //   }
      //   if (this.solve(false) == 0) this.seedBoard();
      // },

      generateBoard(difficulty: number) {
        // reset board
        this.won = false;
        for (let row = 0; row < 9; row++) {
          for (let col = 0; col < 9; col++) {
            this.grid[row][col] = 0;
            this.mutable[row][col] = false;
          }
        }

        // creates a random solved board
        // this.seedBoard();
        this.solve(true);

        // random array of all indices
        let indices = Array();
        for (let row = 0; row < 9; row++) {
          for (let col = 0; col < 9; col++) {
            indices.push([row, col]);
          }
        }
        indices = indices.sort((a, b) => 0.5 - Math.random());

        // remove cells from grid x times (x = difficulty)
        this.numFilled = 81 - difficulty;
        let i = 0;
        while (difficulty > 0 && i < 81) {
          let val = this.grid[indices[i][0]][indices[i][1]];
          this.grid[indices[i][0]][indices[i][1]] = 0;
          
          if (this.solve(false) != 1) {
            this.grid[indices[i][0]][indices[i][1]] = val;
          } else {
            difficulty--
            this.mutable[indices[i][0]][indices[i][1]] = true;
          }
          i++;
        }

        // if no board correctly generated
        if (difficulty > 0) this.generateBoard(difficulty);
      }
    }
  }
</script>

<template>

  <div style="padding: 50px; display: flex; justify-content: center;">
    <button @click="generateBoard(20)">Easy</button>
    <button @click="generateBoard(40)">Medium</button>
    <button @click="generateBoard(60)">Hard</button>
  </div>

  <div class="sudoku-board" @keydown="updateNum($event)" tabindex="-1">
    <div v-for="row in 9" class="sudoku-row">
      <SudokuCell v-for="col in 9" :row="row-1" :col="col-1" :val="grid[row-1][col-1]"
      :class="{ active: currRow == row-1 && currCol == col-1, immutable: !mutable[row-1][col-1]}"
      @click="currRow = row-1; currCol = col-1;"/>
    </div>
  </div>

  <div class="text" v-if="won">You win!</div>
  <div class="text" style="color: red;" v-else>Incomplete/incorrect board</div>

</template>

<style scoped>
button {
  padding: 20px 50px;
  margin: 10px;
  text-align: center;
  border: solid 1px white;
  border-radius: 15px;
  background-color: darkturquoise;
  color: white;
}

.sudoku-board {
  justify-items: center;
}

div:focus {
  outline: 0px;
}

.sudoku-row {
  /* border: 1px solid red; */
  display: flex;
}

.text {
  text-align: center;
  padding: 20px;
  color: greenyellow;
  font-size: xx-large;
}
</style>