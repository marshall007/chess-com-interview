<script lang="ts">
  import { Chessground,  } from 'svelte-chessground';
  import type { Config } from 'chessground/config';
	import type { Key } from 'chessground/types';

  let chessground: Chessground;
  let lastMove: string;
  let moves: Key[] = [];

  const config: Config = {
    fen: '8/8/8/8/8/8/8/8 w - - 0 1',
    draggable: { enabled: false },
    drawable: {
      enabled: false,
    },
    events: {
      select(key) {
        if (lastMove !== key) {
          moves = [...moves, key];
          lastMove = key;
          chessground.setAutoShapes([{ orig: key, brush: 'green' }]);
        }
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
