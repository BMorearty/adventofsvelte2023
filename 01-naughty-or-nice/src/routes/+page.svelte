<script lang="ts">
	import type { Child } from './types';
	import { onMount, tick } from 'svelte';
	import { crossfade } from 'svelte/transition';
	import Children from './Children.svelte';

	let children: Child[];
	let niceChildren: Child[];
	let naughtyChildren: Child[];
	let errorMessage: string;
	let name: string;
	let timeout: number;
	let lastId = 0;
	const [send, receive] = crossfade({});

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
				children = json.map((child: Child) => ({ ...child, id: ++lastId }));
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

	async function addChild() {
		children = [{ name, tally: 0, id: ++lastId }, ...children];
		updateChildren();
		name = '';
		await tick();
		window.scrollTo({ top: document.body.scrollHeight, behavior: 'smooth' });
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
			<th>Nice children</th>
			<th>Naughty children</th>
		</tr>
		<tr>
			<td style="vertical-align: top">
				<Children list="nice" children={niceChildren} {send} {receive} {naughtyOrNice} />
			</td>
			<td style="vertical-align: top">
				<Children list="naughty" children={naughtyChildren} {send} {receive} {naughtyOrNice} />
			</td>
		</tr>
	</table>
{/if}

<style>
	:global(body) {
		background-color: #333;
		color: #ccc;
		font-family: Arial, Helvetica, sans-serif;
		width: 420px;
		margin: 0 auto;
	}
	h1 {
		margin-left: 5px;
	}
	form {
		margin-left: 5px;
		margin-bottom: 20px;
	}
	.error {
		color: red;
	}
</style>
