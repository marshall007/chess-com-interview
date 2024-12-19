<script setup lang="ts">
import { onMounted, ref } from 'vue';

import { Chessground } from 'chessground';
import type { Api } from 'chessground/api';
import type { Config } from 'chessground/config';
import type { Key } from 'chessground/types';

export type BoardConfig = Omit<Config, 'events'>;

export type { Api as Board, Key };

const props = defineProps<{
  config: Config;
}>();
const emit = defineEmits<{
  board: [value: Api];
  select: [board: Api, square: Key];
}>();

let board: Api;
const el = ref<HTMLElement | null>(null);

onMounted(() => {
  if (el.value == null) {
    throw new Error('Failed to mount board to DOM element.');
  }

  const config: Config = {
    ...props.config,
    events: {
      select(...args) {
        emit('select', board, ...args);
      },
    },
  };

  board = Chessground(el.value, config);
  emit('board', board);
});
</script>

<template>
  <div>
    <div class="cg-wrap" ref="el"></div>
  </div>
</template>

<style>
.cg-wrap {
  width: 100%;
  height: 100%;
}
</style>
<style src="~/chessground/assets/chessground.base.css"></style>
<style src="~/chessground/assets/chessground.brown.css"></style>
