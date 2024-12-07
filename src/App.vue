<script setup lang="ts">
import { ref } from 'vue';

import type { Key } from 'chessground/types';
import type { Api } from 'chessground/api';
import type { Config } from 'chessground/config';

import Chessboard from '@/components/Chessboard.vue';

let board: Api;

let lastMove: string;
const moves = ref<Key[]>([]);

const config: Config = {
  fen: '8/8/8/8/8/8/8/8 w - - 0 1',
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
  <Chessboard class="board" :config="config" @ready="(api) => (board = api)" />
  <aside class="moves">
    <h4>Total Moves: {{ moves.length }}</h4>
    <ol>
      <li v-for="(move, index) in moves" v-bind:key="index">{{ move }}</li>
    </ol>
  </aside>
</template>

<style>
.moves {
  overflow-y: scroll;
}
</style>
