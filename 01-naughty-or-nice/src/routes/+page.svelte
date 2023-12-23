<script lang="ts">
	import type { Child } from './types';
	import ShowChild from './ShowChild.svelte';
	import { onMount } from 'svelte';

	let children: Child[];
	let niceChildren: Child[];
	let naughtyChildren: Child[];
	let errorMessage: string;
	let name: string;
	let timeout: number;

	function updateChildren() {
		timeout = 0;
		niceChildren = children?.filter((child) => child.tally >= 0).sort((a, b) => b.tally - a.tally);
		naughtyChildren = children
			?.filter((child) => child.tally < 0)
			.sort((a, b) => a.tally - b.tally);
	}

	onMount(() => {
		fetch('https://advent.sveltesociety.dev/data/2023/day-one.json')
			.then((response) => response.json())
			.then((json) => {
				children = json;
				updateChildren();
			})
			.catch(() => (errorMessage = 'Unable to get naughty or nice data!'));

		updateChildren();
	});

	function naughtyOrNice(list: Child[], i: number, offset: number) {
		list[i].tally += offset;
		niceChildren = niceChildren;
		naughtyChildren = naughtyChildren;

		if (timeout) {
			clearTimeout(timeout);
		}
		timeout = setTimeout(updateChildren, 1000);
	}

	function addChild() {
		children = [{ name, tally: 0 }, ...children];
	}
</script>

<h1>Naughty or nice counter</h1>

{#if errorMessage}
	<p class="error">{errorMessage}</p>
{/if}
{#if children}
	<form action="GET" on:submit|preventDefault={addChild}>
		<input type="text" placeholder="Name" bind:value={name} name="name" />
		<input type="submit" value="Add a child" />
	</form>
	<table>
		<tr>
			<td style="vertical-align: top">
				<table class="nice">
					{#each niceChildren as child, i}
						<ShowChild {child} list={niceChildren} onclick={naughtyOrNice} {i} />
					{/each}
				</table>
			</td>
			<td style="vertical-align: top">
				<table class="naughty">
					{#each naughtyChildren as child, i}
						<ShowChild {child} list={naughtyChildren} onclick={naughtyOrNice} {i} />
					{/each}
				</table></td
			>
		</tr>
	</table>
{/if}

<style>
	.error {
		color: red;
	}
	.nice {
		background-color: greenyellow;
	}
	.naughty {
		background-color: hotpink;
	}
</style>
