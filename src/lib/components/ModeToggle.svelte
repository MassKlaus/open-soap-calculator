<script>
	import { toggleMode } from 'mode-watcher';
	import Icon from '@iconify/svelte';
	import Button from './ui/button/button.svelte';
</script>

<Button on:click={toggleMode} variant="outline" size="icon">
	<Icon
		icon="material-symbols:sunny"
		class="h-[1.2rem] w-[1.2rem] rotate-0 scale-100 transition-all dark:-rotate-90 dark:scale-0"
	/>
	<Icon
		icon="material-symbols:dark-mode"
		class="absolute h-[1.2rem] w-[1.2rem] rotate-90 scale-0 transition-all dark:rotate-0 dark:scale-100"
	/>
	<span class="sr-only">Toggle theme</span>
</Button>
