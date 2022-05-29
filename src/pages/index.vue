<script setup lang="ts">
interface BlockState {
  x: number
  y: number
  revealed: boolean
  mine?: boolean
  flagged?: boolean
  adjacentMines: number
}
const WIDTH = 10
const HEIGHT = 10

const store = reactive(Array.from({ length: WIDTH }, (_, y) => {
  return Array.from({ length: HEIGHT }, (_, x): BlockState => ({
    x,
    y,
    adjacentMines: 0,
    revealed: false,
    flagged: false,
  }))
}))

// 定义随机炸弹
function generateMInes() {
  for (const row of store) {
    for (const block of row)
      block.mine = Math.random() < 0.1
  }
}

const directions = [
  [-1, -1],
  [-1, 0],
  [-1, 1],
  [0, -1],
  [0, 1],
  [1, -1],
  [1, 0],
  [1, 1],
]
const numberColors = [
  'text-gray-100',
  'text-orange-500',
  'text-yellow',
  'text-green-500',
]

// 计算周围炸弹
function updateNumber() {
  store.forEach((raw, y) => {
    raw.forEach((block, x) => {
      if (block.mine)
        return
      directions.forEach(([dx, dy]) => {
        const X2 = x + dx
        const y2 = y + dy
        if (X2 < 0 || X2 >= WIDTH || y2 < 0 || y2 >= HEIGHT)
          return
        if (store[y2][X2].mine)
          block.adjacentMines += 1
      })
    })
  })
}

function getBlockClass(block: BlockState) {
  if (!block.revealed)
    return 'bg-gray-500/10'
  return block.mine ? 'bg-red-500/30' : numberColors[block.adjacentMines]
}

function onClick(block: BlockState) {
  const { x, y } = block
  store[y][x].revealed = true
  if (block.mine)
    alert('游戏结束')
}
generateMInes()
updateNumber()
</script>

<template>
  <div>
    Minesweeper
    <div p8>
      <div v-for="row, y in store" :key="y" flex="~" justify-center items-center>
        <button
          v-for="item, x in row" :key="x" :class="getBlockClass(item)" w-10 h-10 border="1 gray-400/10" m=".3"
          items-center hover="bg-gray/10" justify-center flex="~" @click="onClick(item)"
        >
          <template v-if="item.revealed">
            <div v-if="item.mine" i-mdi:minecraft />
            <div v-else>
              {{ item.adjacentMines }}
            </div>
          </template>
        </button>
      </div>
    </div>
  </div>
</template>
