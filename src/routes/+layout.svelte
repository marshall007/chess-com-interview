<script lang="ts">
	import '../app.scss';
	let { children } = $props();
</script>

<div class="app">
	{@render children()}
</div>
