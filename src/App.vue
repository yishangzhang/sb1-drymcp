<script setup>
import { ref, computed } from 'vue'

const boardSize = 15
const board = ref(Array(boardSize).fill().map(() => Array(boardSize).fill(null)))
const currentPlayer = ref('black')
const winner = ref(null)

const gameStatus = computed(() => {
  if (winner.value) {
    return `${winner.value.toUpperCase()} wins!`
  }
  return `Current player: ${currentPlayer.value.toUpperCase()}`
})

function placeStone(row, col) {
  if (!board.value[row][col] && !winner.value) {
    board.value[row][col] = currentPlayer.value
    if (checkWin(row, col)) {
      winner.value = currentPlayer.value
    } else {
      currentPlayer.value = currentPlayer.value === 'black' ? 'white' : 'black'
    }
  }
}

function checkWin(row, col) {
  const directions = [
    [1, 0], [0, 1], [1, 1], [1, -1]
  ]
  
  for (const [dx, dy] of directions) {
    let count = 1
    for (let i = 1; i < 5; i++) {
      const newRow = row + i * dx
      const newCol = col + i * dy
      if (newRow < 0 || newRow >= boardSize || newCol < 0 || newCol >= boardSize || board.value[newRow][newCol] !== currentPlayer.value) {
        break
      }
      count++
    }
    for (let i = 1; i < 5; i++) {
      const newRow = row - i * dx
      const newCol = col - i * dy
      if (newRow < 0 || newRow >= boardSize || newCol < 0 || newCol >= boardSize || board.value[newRow][newCol] !== currentPlayer.value) {
        break
      }
      count++
    }
    if (count >= 5) {
      return true
    }
  }
  return false
}

function resetGame() {
  board.value = Array(boardSize).fill().map(() => Array(boardSize).fill(null))
  currentPlayer.value = 'black'
  winner.value = null
}
</script>

<template>
  <div class="gomoku-game">
    <h1>Gomoku (五子棋)</h1>
    <div class="game-status">{{ gameStatus }}</div>
    <div class="board">
      <div v-for="(row, rowIndex) in board" :key="rowIndex" class="board-row">
        <div v-for="(cell, colIndex) in row" :key="colIndex" 
             class="board-cell" 
             @click="placeStone(rowIndex, colIndex)">
          <div v-if="cell" :class="['stone', cell]"></div>
        </div>
      </div>
    </div>
    <button @click="resetGame" class="reset-button">Reset Game</button>
  </div>
</template>

<style scoped>
.gomoku-game {
  display: flex;
  flex-direction: column;
  align-items: center;
  font-family: Arial, sans-serif;
}

.game-status {
  margin-bottom: 20px;
  font-size: 1.2em;
  font-weight: bold;
}

.board {
  display: inline-grid;
  grid-template-columns: repeat(15, auto);
  gap: 1px;
  background-color: #dcb35c;
  padding: 10px;
  border: 2px solid #8b4513;
}

.board-cell {
  width: 30px;
  height: 30px;
  background-color: #dcb35c;
  display: flex;
  justify-content: center;
  align-items: center;
  position: relative;
}

.board-cell::before,
.board-cell::after {
  content: '';
  position: absolute;
  background-color: #000;
}

.board-cell::before {
  width: 100%;
  height: 1px;
  top: 50%;
  transform: translateY(-50%);
}

.board-cell::after {
  width: 1px;
  height: 100%;
  left: 50%;
  transform: translateX(-50%);
}

.stone {
  width: 26px;
  height: 26px;
  border-radius: 50%;
  z-index: 1;
}

.black {
  background-color: #000;
}

.white {
  background-color: #fff;
  border: 1px solid #000;
}

.reset-button {
  margin-top: 20px;
  padding: 10px 20px;
  font-size: 1em;
  background-color: #4CAF50;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.reset-button:hover {
  background-color: #45a049;
}
</style>