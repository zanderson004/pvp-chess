<!-- SudokuCell.vue -->

<script lang="ts">
  export default {
    name: 'SudokuCell',
    // Add data, methods, or other component options here
  }
  let val = -1;
</script>

<template>
    <div class="sudoku-cell">
      -1
    </div>
</template>
  
<style scoped>
  .sudoku-cell {
    width: 40px;
    height: 40px;
    border: 1px solid #ffffff;
    display: inline-flex;
    justify-content: center;
    align-items: center;
  }
</style>