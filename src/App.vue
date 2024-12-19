<script setup lang="ts">
import { nextTick, ref, useTemplateRef } from 'vue';

import ChessBoard from '@/components/ChessBoard.vue';
import type { Board, BoardConfig, Key } from '@/components/ChessBoard.vue';

const moveList = useTemplateRef('moveList');
const moves = ref<Key[]>([]);

const config: BoardConfig = {
  fen: '8/8/8/8/8/8/8/8 w - - 0 1', // empty board
  coordinates: false,
  draggable: { enabled: false },
  drawable: { enabled: false },
};

async function onSelect(board: Board, square: Key) {
  const lastMove = moves.value[moves.value.length - 1];

  if (lastMove !== square) {
    const moveIndex = moves.value.push(square);
    board.setShapes([
      {
        orig: square,
        brush: 'green',
        label: {
          text: moveIndex.toString(),
          fill: 'rgba(102,102,102,0.6)',
        },
      },
    ]);

    await nextTick();
    if (moveList?.value) {
      moveList.value.$el.scrollTop = moveList.value.$el.scrollHeight;
    }
  }
}
</script>

<template>
  <main class="board">
    <ChessBoard class="square" :config @select="onSelect" />
  </main>
  <aside class="moves">
    <h3>Total Moves: {{ moves.length }}</h3>
    <TransitionGroup name="list" tag="ol" ref="moveList">
      <li v-for="(move, index) in moves" :key="index">{{ move }}</li>
    </TransitionGroup>
  </aside>
</template>

<style lang="scss" scoped>
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
  --move-grid-width: 2;

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
    // render the list as a grid to better utilize horizontal space
    display: grid;
    grid-template-columns: repeat(var(--move-grid-width), 1fr);
    grid-template-rows: repeat(var(--move-grid-width), 1fr);

    padding: 0;
    flex: 0 auto;
    overflow-y: scroll;

    list-style: none;
    counter-reset: move-counter;

    li {
      border-bottom: 1px solid var(--color-border);
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

      // animate items as they are appended
      transition: 0.5s ease;
      transition-property: background-color, color;

      &.list-enter-from {
        background: rgba(255, 255, 255, 0.8);
        color: #666;
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
    --move-grid-width: 4;

    // in portrait mode, bottom (sidebar) panel should consume
    // available space below board
    flex: 80%;
    margin-top: 0;
  }
}
</style>
