<template>
  <main class="pt-8 text-center">
    <h1 class="mb-8 text-3xl font-bold uppercase">Tic Tac Toe</h1>

    <h3 class="text-xl mb-4">{{ currentPlayerName }}'s turn</h3>

    <div class="flex flex-col items-center mb-8">
      <div 
        v-for="(row, x) in board" 
        :key="x"
        class="flex">
        <div 
          v-for="(cell, y) in row" 
          :key="y" 
          @click="() => MakeMove(x, y)" 
          :class="`border border-white w-24 h-24 hover:bg-gray-600 flex items-center justify-center text-4xl cursor-pointer transition-transform duration-200 ${cell === 'x' ? 'text-red-500' : 'text-blue-500'} ${winner && cell === winner ? 'bg-green-500' : ''}`">
          {{ cell === 'x' ? 'X' : cell === 'o' ? 'O' : '' }}
        </div>
      </div>
    </div>

    <div class="text-center">
      <h2 v-if="winner" class="text-6xl font-bold mb-8">{{ winnerName }} wins!</h2>
      <h2 v-if="!winner && isBoardFull()" class="text-6xl font-bold mb-8">It's a draw!</h2>
      <button @click="ResetGame" class="px-4 py-2 bg-black text-white rounded uppercase font-bold hover:bg-gray-800 duration-300 hover:bg-white hover:text-black">Reset</button>
    </div>
  </main>
</template>

<script setup>
import { ref, computed } from 'vue';

const player = ref('x');
const aiPlayer = ref('o');
const playerName = ref('Player 1');
const aiPlayerName = ref('AI Opponent');
const currentPlayerName = computed(() => player.value === 'x' ? playerName.value : aiPlayerName.value);
const winnerName = computed(() => winner.value === 'x' ? playerName.value : aiPlayerName.value);

const board = ref([
  ['', '', ''],
  ['', '', ''],
  ['', '', '']
]);

const calculateWinner = (squares) => {
  const lines = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6]
  ];
  for (let i = 0; i < lines.length; i++) {
    const [a, b, c] = lines[i];
    if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
      return squares[a];
    }
  }
  return null;
};

const winner = computed(() => {
  return calculateWinner(board.value.flat());
});

const isBoardFull = () => {
  return board.value.flat().every(cell => cell !== '');
};

const MakeMove = (x, y) => {
  if (winner.value || board.value[x][y] !== '') return;

  board.value[x][y] = player.value;

  if (!winner.value && !isBoardFull()) {
    setTimeout(AImakeMove, 500);
  }
};

const AImakeMove = () => {
  const emptySquares = [];
  for (let i = 0; i < 3; i++) {
    for (let j = 0; j < 3; j++) {
      if (board.value[i][j] === '') {
        emptySquares.push({ x: i, y: j });
      }
    }
  }

  if (emptySquares.length > 0) {
    const randomMove = emptySquares[Math.floor(Math.random() * emptySquares.length)];
    board.value[randomMove.x][randomMove.y] = aiPlayer.value;

    if (calculateWinner(board.value.flat())) {
      winner.value = aiPlayer.value;
    }
  }
};

const ResetGame = () => {
  board.value = [
    ['', '', ''],
    ['', '', ''],
    ['', '', '']
  ];
  player.value = 'x';
};

</script>

<style>
body {
  background-color: #000000; 
  color: white;
}

.border {
  border-color: white;
  border-width: 2.5px;
}

.hover\:bg-gray-600:hover {
  background-color: #6c6c6c;
}

.text-red-500 {
  color: #ff4500;
}

.text-blue-500 {
  color: #1e90ff;
}

.bg-green-500 {
  background-color: #6cc882;
}

button {
  border: 2px solid white;
}

button:hover {
  background-color: white;
  color: black;
}

</style>
