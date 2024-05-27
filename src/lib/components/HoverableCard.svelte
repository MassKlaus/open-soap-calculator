<script lang="ts">
	import Icon from '@iconify/svelte';
	import { createEventDispatcher } from 'svelte';

	export let icon: string;
	export let active: boolean = false;

	const dispatch = createEventDispatcher();
</script>

<button
	class="flex cursor-pointer {active? 'bg-muted outline outline-2' : ''} items-center gap-4 rounded-md border px-6 py-4 text-left shadow-sm hover:bg-accent hover:text-accent-foreground"
	on:click={() => dispatch('click')}
>
	<Icon {icon} class="h-7 w-7" />
	<div>
		<h4 class="scroll-m-20 text-xl font-semibold tracking-tight">
			<slot name="title" />
		</h4>
		<p class="text-sm text-muted-foreground">
			<slot name="description" />
		</p>
	</div>
</button>
