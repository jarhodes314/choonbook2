<script lang="ts">
	import { page } from '$app/stores';
	import Tune from '$lib/Tune.svelte';
	import Set from '$lib/Set.svelte';
	export let data;
</script>

{#if data.type.content[0].type == 'set'}
	<h2>Setlist</h2>
	<div>
		{#each data.type.content as set}
			<Set
				title={set.name}
				tunes={set.content.map((tune) => ({ abc: tune.abc }))}
				href="{$page.url}{set.slug}"
			/>
		{/each}
	</div>
{:else}
	<h2>{data.type.name}</h2>
	{#each data.type.content as tune}
		<Tune abc={tune.abc} />
	{/each}
{/if}

<footer>
	Site made with ❤ by <a href="https://flyingcat.dance/services#websites">Flying Cat</a>
</footer>

<style>
	footer {
		text-align: center;
		align-self: center;
		margin-bottom: 1em;
		font-size: small;
		padding: 1em;
		color: #555;
	}

	div {
		display: flex;
		flex-wrap: wrap;
		justify-content: space-evenly;
	}
</style>
