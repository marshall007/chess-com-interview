<script setup lang="ts">
import { ref } from 'vue';

import ChessBoard, { type Board, type BoardConfig, type Key } from '@/components/ChessBoard.vue';

let lastMove: string;
const moves = ref<Key[]>([]);

const config: BoardConfig = {
  fen: '8/8/8/8/8/8/8/8 w - - 0 1', // empty board
  coordinates: false,
  draggable: { enabled: false },
  drawable: { enabled: false },
};

function onSelect(board: Board, square: Key) {
  if (lastMove !== square) {
    moves.value.push(square);
    lastMove = square;
    board.setShapes([{ orig: square, brush: 'green' }]);
  }
}
</script>

<template>
  <main class="board">
    <ChessBoard class="square" :config="config" @select="onSelect" />
  </main>
  <aside class="moves">
    <h3>Total Moves: {{ moves.length }}</h3>
    <ol>
      <li v-for="(move, index) in moves" v-bind:key="index">{{ move }}</li>
    </ol>
  </aside>
</template>

<style lang="scss">
.board {
  flex: 80%;
  margin: var(--section-gap);
}

.square {
  // `aspect-ratio` is doing the heavy lifting to ensure chessboard
  // consumes avaiable space while retaining a square shape
  aspect-ratio: 1 / 1;
  max-height: 100%;
  max-width: 100%;
  margin: 0 auto;
}

.moves {
  flex: 20%;
  display: flex;
  flex-direction: column;
  margin: var(--section-gap);
  min-height: 0; // prevent overflowing viewport in portrait mode

  h3 {
    flex: 0;
    margin-bottom: var(--section-gap);
    font-weight: bold;
  }

  ol {
    padding: 0;
    flex: 0 auto;
    overflow-y: scroll;

    list-style: none;
    counter-reset: move-counter;

    li {
      counter-increment: move-counter;
      font-weight: bold;
      padding: 2px;

      &:before {
        // custom list-style counter so we can set background color
        // and control text alignment
        content: counter(move-counter) '. ';
        display: inline-block;
        font-weight: normal;
        margin-right: 10px;
        text-align: right;
        width: 35px;
      }

      &:nth-child(odd) {
        background: rgba(255, 255, 255, 0.1);
      }

      &:not(:last-child) {
        border-bottom: 1px solid var(--color-border);
      }
    }
  }
}

@media (orientation: portrait) {
  .board {
    // leave some space for move list as viewport
    // dimensions approach square shape
    max-height: 80%;
  }

  .moves {
    // in portrait mode, bottom (sidebar) panel should consume
    // available space below board
    flex: 80%;
    margin-top: 0;

    ol {
      // render the list as a grid to better utilize horizontal space
      display: grid;
      grid-template-columns: repeat(4, 1fr);
      grid-template-rows: repeat(4, 1fr);
    }
  }
}
</style>
