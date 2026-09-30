<template>
  <div class="game">
    <h1>Tic Tac Toe</h1>
    <div class="mode-selection">
      <button 
        @click="setMode('twoPlayer')"
        :class="{ selected: mode === 'twoPlayer' }"
      >
        Two Players
      </button>
      <button 
        @click="setMode('vsAI')"
        :class="{ selected: mode === 'vsAI' }"
      >
        Play vs AI
      </button>
    </div>
    <p v-if="!mode" class="status select-mode">
      Select a mode to start the game
    </p>
    <div class="board" :class="{ disabled: winner || !mode }">
      <button
        v-for="(cell, index) in board"
        :key="index"
        class="cell"
        :class="[cell, { win: winningCells.includes(index) }]"
        @click="makeMove(index)"
        :disabled="cell || winner || !mode || (mode === 'vsAI' && currentPlayer === 'O')"
      >
        {{ cell }}
      </button>
    </div>
    <p v-if="winner" class="status winner">🎉 Player {{ winner }} wins!</p>
    <p v-else-if="isTie" class="status tie">🤝 It's a tie! Play again.</p>
    <p v-else-if="mode" class="status" :class="currentPlayer === 'X' ? 'turn-x' : 'turn-o'">
      Player {{ currentPlayer }}'s turn
    </p>
    <button v-if="mode" class="reset" @click="resetGame">Reset Game</button>
  </div>
</template>
<script>
export default {
  name: "TicTacToe",
  data() {
    return {
      board: Array(9).fill(null),
      currentPlayer: "X",
      winner: null,
      winningCells: [],
      mode: null,
    };
  },
  computed: {
    isTie() {
      return this.board.every(cell => cell) && !this.winner;
    },
  },
  methods: {
    setMode(selectedMode) {
      this.mode = selectedMode;
      this.resetGame();
      if (this.mode === "vsAI" && this.currentPlayer === "O") {
        setTimeout(() => this.aiMove(), 600);
      }
    },
    makeMove(index) {
      if (this.board[index] || this.winner || !this.mode) return;
      this.board[index] = this.currentPlayer;
      if (this.checkWinner()) {
        this.winner = this.currentPlayer;
      } else {
        if (this.mode === "vsAI") {
          this.currentPlayer = "O";
          setTimeout(() => this.aiMove(), 600);
        } else {
          this.currentPlayer = this.currentPlayer === "X" ? "O" : "X";
        }
      }
    },
    aiMove() {
      const emptyCells = this.board
        .map((cell, index) => (cell === null ? index : null))
        .filter(i => i !== null);
      if (!emptyCells.length || this.winner) return;
      const randomIndex = emptyCells[Math.floor(Math.random() * emptyCells.length)];
      this.board[randomIndex] = this.currentPlayer;
      if (this.checkWinner()) {
        this.winner = this.currentPlayer;
      } else {
        this.currentPlayer = "X"; 
      }
    },
    checkWinner() {
      const wins = [
        [0, 1, 2],
        [3, 4, 5],
        [6, 7, 8],
        [0, 3, 6],
        [1, 4, 7],
        [2, 5, 8],
        [0, 4, 8],
        [2, 4, 6],
      ];
      for (const [a, b, c] of wins) {
        if (
          this.board[a] &&
          this.board[a] === this.board[b] &&
          this.board[a] === this.board[c]
        ) {
          this.winningCells = [a, b, c];
          return true;
        }
      }
      return false;
    },
    resetGame() {
      this.board = Array(9).fill(null);
      this.currentPlayer = "X";
      this.winner = null;
      this.winningCells = [];
    },
  },
};
</script>
<style scoped>
h1 {
  color: #020815ff;
  font-size: 3rem;
  font-weight: bold;
}
.game {
  display: flex;
  flex-direction: column;
  align-items: center;
  max-width: 320px;
  margin: auto;
  padding: 20px;
  background: #e0f7fa;
  border-radius: 15px;
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.1);
  font-family: Arial, sans-serif;
}
.mode-selection {
  display: flex;
  gap: 10px;
  margin-bottom: 15px;
}
.mode-selection button {
  padding: 10px 20px;
  border: none;
  border-radius: 25px;
  background: #4469baff;
  color: white;
  font-weight: bold;
  cursor: pointer;
  transition: all 0.2s ease;
}
.mode-selection button:hover {
  transform: scale(1.05);
}
.mode-selection button.selected {
  background: #0050a0ff;
  transform: scale(1.1);
  box-shadow: 0 4px 12px rgba(0,0,0,0.3);
}
.board {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 8px;
  margin: 20px 0;
  padding: 12px;
  background: linear-gradient(145deg, #79c9ce, #a0e1e6);
  border-radius: 15px;
}
.board.disabled {
  pointer-events: none;
  opacity: 0.95;
}
.cell {
  width: 90px;
  height: 90px;
  font-size: 2.2rem;
  border: 2px solid #333;
  border-radius: 12px;
  cursor: pointer;
  background-color: white;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  transition: all 0.2s ease;
}
.cell:hover {
  background-color: #cce7ff;
  transform: scale(1.05);
}
.board.disabled .cell:hover {
  transform: none;
}
.cell:disabled {
  cursor: not-allowed;
  background-color: #e5e7eb;
  color: #6b7280;
}
.cell.X {
  color: #0b4ac0;
  font-weight: bold;
}
.cell.O {
  color: #1fa81f;
  font-weight: bold;
}
.cell.win {
  background: linear-gradient(145deg, #006a04ff, #007f06ff);
  color: white;
  box-shadow: 0 0 15px rgba(82, 119, 83, 0.8);
}
.turn-x {
  color: #d32f2f;
}
.turn-o {
  color: #1976d2;
}
.status {
  font-size: 1.2rem;
  font-weight: bold;
  margin-bottom: 10px;
  text-align: center;
}
.status.winner {
  color: #104503;
  font-size: 1.5rem;
}
.status.tie {
  color: #ff9800;
  font-size: 1.5rem;
}
.select-mode {
  color: #ff5722;
}
.reset {
  margin-top: 10px;
  padding: 12px 20px;
 
  border-radius: 25px;
  background: linear-gradient(145deg, #2563eb, #1e40af);
  color: white;
  font-size: 16px;
  cursor: pointer;
  transition: all 0.2s ease;
}
</style>