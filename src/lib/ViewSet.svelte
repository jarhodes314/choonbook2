<script>
	import { browser } from '$app/environment';
	import { renderAbc } from 'abcjs';
	import KeySelect from '$lib/KeySelect.svelte';
	import Tune from '$lib/Tune.svelte';
	import { writable } from 'svelte/store';

	let visualTranspose = 0;
	export let set;

	let visible = [...Array(set.content.length)];
	let displayFrom = [0];
	$: visible = displayFrom && visible;
	$: visible[displayFrom[displayFrom.length - 1]] =
		visible[displayFrom[displayFrom.length - 1]] || true;
	let refreshVisibility = 0;
	// $: displayFrom && refreshVisibility++;

	let offsets = new Map();

	$: for (let tune of set.content) {
		const abcDetails = (browser || null) && renderAbc('*', tune.abc, { visualTranspose: 0 })[0];
		tune.originalKey = abcDetails?.getKeySignature();
		abcDetails?.metaText.title;
		tune.offset = writable(0);
	}
</script>

{#each set.content as tune, i}
	{#if i >= displayFrom[displayFrom.length - 1]}
		<div class="visible-{visible[i]} tune">
			{#if tune.originalKey}
				<KeySelect bind:transposition={visualTranspose} originalKey={tune.originalKey} />
			{/if}
			<button on:click={() => tune.offset.update((offset) => offset - 12)}>Down an octave</button>
			<button on:click={() => tune.offset.update((offset) => offset + 12)}>Up an octave</button>
			<Tune
				abc={tune.abc}
				{visualTranspose}
				tuneOffset={tune.offset}
				bind:visible={visible[i]}
				{refreshVisibility}
			/>
		</div>
	{/if}
{/each}

{#if displayFrom.length > 1}
	<button
		on:click={() => {
			window.scrollBy(0, -25);
			displayFrom.pop();
			displayFrom = displayFrom;
		}}
		class="hidden back"><div /></button
	>{:else}<button disabled class="hidden back" />
{/if}
{#if !visible[visible.length - 1]}
	<button
		on:click={() => (displayFrom = [...displayFrom, visible.indexOf(false)])}
		class="hidden next"><div /></button
	>
{:else}
	<button class="hidden next" disabled />
{/if}

<style>
	.visible-null,
	.visible-false {
		visibility: hidden;
	}
	.visible-false {
		height: 0;
		overflow: hidden;
	}
	.tune {
		width: 90%;
		margin: auto;
	}

	button.hidden {
		position: fixed;
		bottom: 0;
		height: 100%;
		width: min(15%, 10vw);
		border: none;
		background: none;
	}

	button.back {
		left: 0;
	}
	button.next {
		right: 0;
	}
	button.hidden div {
		width: 0;
		height: 0;
		border-top: 60px solid transparent;
		border-bottom: 60px solid transparent;
	}
	button.hidden.back div {
		border-right: 60px solid lightgray;
		position: absolute;
		left: 0.5em;
	}
	button.hidden.next div {
		border-left: 60px solid lightgray;
		position: absolute;
		right: 0.5em;
	}
</style>
