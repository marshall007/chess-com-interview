<script setup lang="ts">
import { ref } from 'vue';

import type { Key } from 'chessground/types';
import type { Api } from 'chessground/api';
import type { Config } from 'chessground/config';

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
  <ChessBoard class="board" :config="config" @ready="(api) => (board = api)" />
  <aside class="moves">
    <h4>Total Moves: {{ moves.length }}</h4>
    <ol>
      <li v-for="(move, index) in moves" v-bind:key="index">{{ move }}</li>
    </ol>
  </aside>
</template>

<style lang="scss">
:root {
  --sidebar-dimension: 200px;
  --board-max-width: calc(100vw - (var(--section-gap) * 2) - var(--sidebar-dimension));
  --board-max-height: calc(90vh - (var(--section-gap) * 2));

  --board-dimension: min(var(--board-max-width), var(--board-max-height));
}

.board {
  height: var(--board-dimension);
  width: var(--board-dimension);
  aspect-ratio: 1 / 1;
  margin: var(--section-gap);
}

.moves {
  flex-grow: 1;
  min-height: 0;
  min-width: var(--sidebar-dimension);
  display: flex;
  flex-direction: column;

  h4 {
    flex: 0;
    margin: var(--section-gap) 0;
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

@media (max-width: 1024px) {
  .board {
    margin: var(--section-gap) auto;
  }
  .moves {
    margin-left: var(--section-gap);
    ol {
      display: grid;
      grid-template-columns: repeat(4, 1fr);
      grid-template-rows: repeat(4, 1fr);
    }
  }
}
</style>
