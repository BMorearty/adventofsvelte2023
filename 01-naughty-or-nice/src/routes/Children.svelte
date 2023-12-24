<script lang="ts">
	import type { Child } from './types';
	import { flip } from 'svelte/animate';

	export let list: 'naughty' | 'nice';
	export let children: Child[];
	export let send;
	export let receive;
	export let naughtyOrNice: (list: Child[], i: number, offset: number) => void;
</script>

<table class:nice={list === 'nice'} class:naughty={list === 'naughty'}>
	{#each children as child, i (child.id)}
		<tr in:receive={{ key: child.id }} out:send={{ key: child.id }} animate:flip>
			<td>{child.name}</td>
			<td>{child.tally}</td>
			<td><button class="naughtyBtn" on:click={() => naughtyOrNice(children, i, -1)}></button></td>
			<td><button class="niceBtn" on:click={() => naughtyOrNice(children, i, 1)}></button></td>
		</tr>
	{/each}
</table>

<style>
	.nice {
		background-color: darkgreen;
	}
	.naughty {
		background-color: darkred;
	}
	table {
		border-radius: 10px;
		margin: 5px;
		padding: 10px;
	}
	button {
		font-size: large;
		background-color: transparent;
		color: #ccc;
		border: transparent;
	}
	.naughtyBtn:after {
		content: '😈';
	}
	.naughtyBtn:active:after {
		content: '👿';
	}
	.niceBtn:after {
		content: '😇';
	}
	.niceBtn:active:after {
		content: '😮';
	}
	.naughtyBtn:active,
	.niceBtn:active {
		transform: scale(1.5);
	}
</style>
