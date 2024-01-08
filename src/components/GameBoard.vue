<template>
  <div>
    <h1>2048</h1>
    <hr />
    <h2>
      Score: <span id="score">{{ score }}</span>
    </h2>
    <div id="board">
      <div v-for="(row, i) in board" :key="i">
        <div v-for="(tile, j) in row" :key="j">
          <div
            class="tile"
            :class="tile <= 4096 ? `x${tile}` : 'x8196'"
            :id="`${i}-${j}`"
          >
            {{ tile }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";

const rows = 4;
const columns = 4;
const score = ref(0);
const board = ref([
  [0, 0, 0, 0],
  [0, 0, 0, 0],
  [0, 0, 0, 0],
  [0, 0, 0, 0],
]);

onMounted(() => {
  document.addEventListener("keyup", (e) => {
    if (e.code == "ArrowLeft") {
      slideUp();
      setTwo();
    } else if (e.code == "ArrowRight") {
      slideDown();
      setTwo();
    } else if (e.code == "ArrowUp") {
      slideLeft();
      setTwo();
    } else if (e.code == "ArrowDown") {
      slideRight();
      setTwo();
    }
  });
  setGame();
});

function setGame() {
  board.value = [
    [0, 0, 0, 0],
    [0, 0, 0, 0],
    [0, 0, 0, 0],
    [0, 0, 0, 0],
  ];
  setTwo();
  setTwo();
}

function hasEmptyTile() {
  for (let r = 0; r < rows; r++) {
    for (let c = 0; c < columns; c++) {
      if (board.value[r][c] == 0) {
        return true;
      }
    }
  }
  return false;
}

function setTwo() {
  if (!hasEmptyTile()) {
    return;
  }
  let found = false;
  while (!found) {
    let r = Math.floor(Math.random() * rows);
    let c = Math.floor(Math.random() * columns);
    if (board.value[r][c] == 0) {
      board.value[r][c] = 2;
      found = true;
    }
  }
}

function filterZero(row) {
  return row.filter((num) => num != 0);
}

function slide(row) {
  row = filterZero(row); //[2, 2, 2]
  for (let i = 0; i < row.length - 1; i++) {
    if (row[i] == row[i + 1]) {
      row[i] *= 2;
      row[i + 1] = 0;
      score.value += row[i];
    }
  }
  row = filterZero(row);
  while (row.length < columns) {
    row.push(0);
  }
  return row;
}

function slideDown() {
  for (let c = 0; c < columns; c++) {
    let row = [
      board.value[0][c],
      board.value[1][c],
      board.value[2][c],
      board.value[3][c],
    ];
    row.reverse();
    row = slide(row);
    row.reverse();

    for (let r = 0; r < rows; r++) {
      board.value[r][c] = row[r];
    }
  }
}

function slideUp() {
  for (let c = 0; c < columns; c++) {
    let row = [
      board.value[0][c],
      board.value[1][c],
      board.value[2][c],
      board.value[3][c],
    ];
    row = slide(row);

    for (let r = 0; r < rows; r++) {
      board.value[r][c] = row[r];
    }
  }
}

function slideLeft() {
  for (let r = 0; r < rows; r++) {
    let row = board.value[r];
    row = slide(row);
    board.value[r] = row;
  }
}

function slideRight() {
  for (let r = 0; r < rows; r++) {
    let row = board.value[r];
    row.reverse();
    row = slide(row);
    board.value[r] = row.reverse();
  }
}
</script>
