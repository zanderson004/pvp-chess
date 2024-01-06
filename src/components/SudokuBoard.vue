<!-- SudokuBoard.vue -->

<script lang="ts">
  import SudokuCell from './SudokuCell.vue';

  export default {
    name: 'SudokuBoard',
    components: {
      SudokuCell
    }
    // Add data, methods, or other component options here
  }
</script>

<template>
  <div class="sudoku-board">
    <div v-for="row in 9" class="sudoku-row">
      <SudokuCell v-for="col in 9" :coords="String(row) + String(col)" />
    </div>
  </div>
</template>

<style scoped>
  .sudoku-row {
    display: block;
  }

  [coords="22"] {
    
  }
</style>