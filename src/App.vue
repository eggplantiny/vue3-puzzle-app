<template>
  <div class="board">
    <template v-for="(tile, index) in tiles" :key="index">
      <div :class="tile === 0 ? 'zero tile' : 'tile'" @click="onClickTile(tile)">
        {{ tile }}
      </div>
    </template>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const tiles = ref<number[]>([1, 2, 3, 4, 5, 6, 7, 8, 9, 0, 10, 12, 13, 14, 11, 15])
const answer = '1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 0'

function findTileIndex (tile: number, squareTiles: number[][]) {
  for (let y = 0; y < squareTiles.length; y += 1) {
    for (let x = 0; x < squareTiles[y].length; x += 1) {
      const targetTile = squareTiles[y][x]
      if (targetTile === tile) {
        return [x, y]
      }
    }
  }

  return [-1, -1]
}

function checkTile (tile: number, squareTiles: number[][]) {
  const [tx, ty] = findTileIndex(tile, squareTiles)
  const [zx, zy] = findTileIndex(0, squareTiles)

  // Up
  if (ty > 0 && (tx === zx) && (ty - 1 === zy)) {
    return true
  }

  // Down
  if (ty < squareTiles.length && (tx === zx) && (ty + 1 === zy)) {
    return true
  }

  // Left
  if (tx > 0 && (ty === zy) && (tx - 1 === zx)) {
    return true
  }

  // Right
  if (tx < squareTiles.length && (ty === zy) && (tx + 1 === zx)) {
    return true
  }

  return false
}

function reflowSquareTiles () {
  return [
    [...tiles.value.slice(0, 4)],
    [...tiles.value.slice(4, 8)],
    [...tiles.value.slice(8, 12)],
    [...tiles.value.slice(12, 16)],
  ]
}

function onClickTile (tile: number) {
  const check = checkTile(tile, reflowSquareTiles())

  if (!check) {
    return
  }

  const targetTileIndex = tiles.value.findIndex(x => x === tile)
  const zeroTileIndex = tiles.value.findIndex(x => x === 0)

  const temp = tiles.value[targetTileIndex]
  tiles.value[targetTileIndex] = 0
  tiles.value[zeroTileIndex] = temp

  checkFinish()
}

async function checkFinish () {
  const myAnswer = tiles.value.join(' ')

  if (answer === myAnswer) {
    await new Promise(resolve => setTimeout(resolve, 100))
    window.alert('You win!')
  }
}
</script>

<style lang="scss">
.board {
  display: grid;
  grid-template-columns: 100px 100px 100px 100px;
  grid-template-rows: 100px 100px 100px 100px;
  grid-gap: 8px;

  .tile {
    display: flex;
    justify-content: center;
    align-items: center;
    color: white;
    font-size: 1.25em;
    font-weight: bold;
    border-radius: 16px;
    background: #7e21d4;
    cursor: pointer;
  }

  .zero {
    visibility: hidden;
  }
}
</style>
