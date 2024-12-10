<script setup lang="ts">
import { ref } from 'vue';

import type { Api } from 'chessground/api';
import type { Config } from 'chessground/config';
import type { Key } from 'chessground/types';

import ChessBoard from '@/components/ChessBoard.vue';

let board: Api;

let lastMove: string;
const moves = ref<Key[]>([]);

const config: Config = {
  fen: '8/8/8/8/8/8/8/8 w - - 0 1',
  coordinates: false,
  draggable: { enabled: false },
  drawable: { enabled: false },
  events: {
    select(key) {
      if (lastMove !== key) {
        moves.value.push(key);
        lastMove = key;
        board.setShapes([{ orig: key, dest: key, brush: 'green' }]);
      }
    },
  },
};
</script>

<template>
  <main class="board">
    <ChessBoard class="square" :config="config" @ready="(api) => (board = api)" />
  </main>
  <aside class="moves">
    <h4>Total Moves: {{ moves.length }}</h4>
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
  max-height: 100%;
  max-width: 100%;
  aspect-ratio: 1 / 1;
}

.moves {
  flex: 20%;
  display: flex;
  flex-direction: column;
  margin: var(--section-gap);
  min-height: 0;

  h4 {
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
      padding: 2px;

      &:before {
        display: inline-block;
        width: 35px;
        content: counter(move-counter) '. ';
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
    max-height: 80%;
  }

  .moves {
    flex: 80%;

    ol {
      display: grid;
      grid-template-columns: repeat(4, 1fr);
      grid-template-rows: repeat(4, 1fr);
    }
  }
}
</style>
