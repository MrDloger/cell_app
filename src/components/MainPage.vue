<template>
  <canvas id="canvas"></canvas>
  <div>
    <button @click="initWorld">обновить</button>
    <button @click="play = !play">старт/пауза</button>
    <button @click="nextState">следующий фрейм</button>
  </div>
  <div>
    <label for="chance">шанс</label>
    <input type="number" id="chance" v-model="chance" step="0.1">
  </div>
</template>

<script setup>
import { onMounted, ref, useTemplateRef } from 'vue'

const play = ref(true);
const chance = ref(0.5)

const SIZE_CELL = 2;
const SIZE_WORLD = { x: 400, y: 400 };
const SIZE_PAD = 0;
const buffer = document.createElement('canvas').getContext('2d');
buffer.canvas.width = SIZE_WORLD.x * SIZE_CELL;
buffer.canvas.height = SIZE_WORLD.y * SIZE_CELL;



const countIteratorDiv = document.getElementById("countIterator")
let ctx = null;
onMounted(() => {
  const canvas = document.getElementById('canvas');
  ctx = canvas.getContext("2d");
  ctx.canvas.width = SIZE_WORLD.x * SIZE_CELL;
  ctx.canvas.height = SIZE_WORLD.y * SIZE_CELL;
  initWorld();
  showWorld();
  run();
});


let world = [];
let countIteratin = 0;

const rect = (x, y, state) => {
  if (state) {
    buffer.fillStyle = 'black';
    buffer.fillRect(x * SIZE_CELL + SIZE_PAD, y * SIZE_CELL + SIZE_PAD, SIZE_CELL - SIZE_PAD, SIZE_CELL - SIZE_PAD);
  }
}
const showWorld = () => {
  buffer.fillStyle = 'white';
  buffer.fillRect(0, 0, SIZE_WORLD.x * SIZE_CELL, SIZE_WORLD.y * SIZE_CELL)
  for (let x = 0; x < SIZE_WORLD.x; x++) {
    for (let y = 0; y < SIZE_WORLD.y; y++) {
      rect(x, y, world[x][y])
    }
  }
  ctx.drawImage(buffer.canvas, 0, 0);
}
const initWorld = () => {
  world = [];
  for (let x = 0; x < SIZE_WORLD.x; x++) {
    let line = [];
    for (let y = 0; y < SIZE_WORLD.y; y++) {
      line.push(Math.random() > chance.value);
    }
    world.push(line);
  }
}
const checkCell = (x, y, state) => {
  let count = 0;
  if (checkState(x - 1, y - 1)) count++;
  if (checkState(x, y - 1)) count++;
  if (checkState(x + 1, y - 1)) count++;
  if (checkState(x - 1, y)) count++;
  if (checkState(x + 1, y,)) count++;
  if (checkState(x - 1, y + 1)) count++;
  if (checkState(x, y + 1)) count++;
  if (checkState(x + 1, y + 1)) count++;
  return state ? count == 2 || count == 3 : count == 3;
}
const checkState = (x, y) => {
  if (x < 0) x = SIZE_WORLD.x - 1;
  if (x >= SIZE_WORLD.x) x = 0;
  if (y < 0) y = SIZE_WORLD.x - 1;
  if (y >= SIZE_WORLD.x) y = 0;
  return world[x][y];

}
const nextState = () => {
  let newState = []
  for (let x = 0; x < SIZE_WORLD.x; x++) {
    let line = []
    for (let y = 0; y < SIZE_WORLD.y; y++) {
      line.push(checkCell(x, y, world[x][y]));
    }
    newState.push(line);
  }
  world = newState;
}
const run = () => {
  setInterval(() => {
    if (play.value) {
      nextState();
    }
    showWorld();
    // countIteratorDiv.innerText = countIteratin++;
  }, 1)
}


</script>


