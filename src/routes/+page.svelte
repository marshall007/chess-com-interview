<script lang="ts">
  import { Chessground,  } from 'svelte-chessground';
  import type { Config } from 'chessground/config';
	import type { Key } from 'chessground/types';

  let chessground: Chessground;
  export let moves: Key[] = [];

  const config: Config = {
    fen: '8/8/8/8/8/8/8/8 w - - 0 1',
    drawable: {
      enabled: false,
    },
    events: {
      select(key) {
        moves = [...moves, key];
      }
    }
  };
</script>

<section class="board">
  <Chessground bind:this={chessground} {config} />
</section>

<section class="sidebar">
  <h4>Moves: {moves.length}</h4>

  <ol>
    {#each moves as move }
      <li>{move}</li>
    {:else}
      Make a move!
    {/each}
  </ol>
</section>
