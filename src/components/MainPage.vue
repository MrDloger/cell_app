<template>
  <canvas id="canvas"></canvas>
</template>

<script setup>
import { onMounted, ref, useTemplateRef } from 'vue'
const SIZE_CELL = 4;
const SIZE_WORLD = { x: 350, y: 250 };
const SIZE_PAD = 0;



const countIteratorDiv = document.getElementById("countIterator")
let ctx = null;
onMounted(() => {
  const canvas = document.getElementById('canvas');
  canvas.setAttribute('width', SIZE_WORLD.x * SIZE_CELL);
  canvas.setAttribute('height', SIZE_WORLD.y * SIZE_CELL);
  ctx = canvas.getContext("2d");
  initWorld();
  showWorld();
  run();
});


let world = [];
let countIteratin = 0;

const rect = (x, y, state) => {
  if (state) {
    ctx.fillStyle = 'black';
    ctx.fillRect(x * SIZE_CELL + SIZE_PAD, y * SIZE_CELL + SIZE_PAD, SIZE_CELL - SIZE_PAD, SIZE_CELL - SIZE_PAD);
  }
}
const showWorld = () => {
  ctx.fillStyle = 'white';
  ctx.fillRect(0, 0, SIZE_WORLD.x * SIZE_CELL, SIZE_WORLD.y * SIZE_CELL)
  for (let x = 0; x < SIZE_WORLD.x; x++) {
    for (let y = 0; y < SIZE_WORLD.y; y++) {
      rect(x, y, world[x][y])
    }
  }
}
const initWorld = () => {
  world = [];
  for (let x = 0; x < SIZE_WORLD.x; x++) {
    let line = [];
    for (let y = 0; y < SIZE_WORLD.y; y++) {
      line.push(Math.random() > 0.9);
    }
    world.push(line);
  }
}
const checkCell = (x, y, state) => {
  let count = 0;
  if (checkState(x - 1, y - 1, state)) count++;
  if (checkState(x, y - 1)) count++;
  if (checkState(x + 1, y - 1, state)) count++;
  if (checkState(x - 1, y, state)) count++;
  if (checkState(x + 1, y, state)) count++;
  if (checkState(x - 1, y + 1, state)) count++;
  if (checkState(x, y + 1, state)) count++;
  if (checkState(x + 1, y + 1, state)) count++;
  return state ? count == 2 || count == 3 : count == 3;
}
const checkState = (x, y, state) => {
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
    ctx.fillStyle = 'white'
    ctx.fillRect(0, 0, SIZE_WORLD.x * SIZE_CELL, SIZE_WORLD.y * SIZE_CELL);
    nextState();
    showWorld();
    // countIteratorDiv.innerText = countIteratin++;
  }, 1)
}


</script>


