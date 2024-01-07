<!-- SudokuCell.vue -->

<script lang="ts">
  export default {
    name: 'SudokuCell',
    // Add data, methods, or other component options here
    props: ["val"]
  }
</script>

<template>
    <div class="sudoku-cell">
      <span v-if="val != 0">{{val}}</span>
    </div>
</template>
  
<style scoped>
  .sudoku-cell {
    width: 70px;
    height: 70px;
    font-size: x-large;
    border: 1px solid grey;
    color: white;
    display: inline-flex;
    justify-content: center;
    align-items: center;
  }

  [row="0"], [row="3"], [row="6"] {
    border-top: 2px solid white;
  }

  [row="8"] {
    border-bottom: 2px solid white;
  }

  [col="0"], [col="3"], [col="6"] {
    border-left: 2px solid white;
  }

  [col="8"] {
    border-right: 2px solid white;
  }

  .active {
    background-color: darkturquoise;
  }

  .immutable {
    font-weight: bold;
    /* color: greenyellow; */
    color: rgb(207, 72, 207);
  }
</style>