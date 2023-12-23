<script lang="ts">
	import type { Child } from './types';

	export let list: Child[];
	export let child: Child;
	export let i: number;
	export let onclick: (list: Child[], i: number, offset: number) => void = () => {};
</script>

<tr>
	<td>{child.name}</td>
	<td>{child.tally}</td>
	<td><button on:click={() => onclick(list, i, -1)}>Naughtier</button></td>
	<td><button on:click={() => onclick(list, i, 1)}>Nicer</button></td>
</tr>
