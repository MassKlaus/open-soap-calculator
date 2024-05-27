<script lang="ts">
	import { ModeWatcher } from 'mode-watcher';
	import '../app.css';
	import ModeToggle from '$lib/components/ModeToggle.svelte';
	import Icon from '@iconify/svelte';
	import { cn } from '$lib/utils';
	import { buttonVariants } from '$lib/components/ui/button';
</script>

<ModeWatcher />

<header
	class="sticky top-0 z-50 w-full border-b border-border/40 bg-background/95 supports-[backdrop-filter]:bg-background/60"
>
	<div class="container flex h-14 max-w-screen-2xl items-center">
		<div class="flex">
			<span class="text-xl font-bold sm:inline-block">Open Soap Calculator</span>
		</div>
		<div class="flex flex-1 items-center space-x-2 justify-end">
			<a
				class={cn(buttonVariants({ variant: 'ghost', size: 'icon' }))}
				href="https://github.com/MassKlaus/open-soap-calculator"
			>
				<Icon icon="mdi:github" class="hover:bg-gray h-8 w-7" />
			</a>
			<ModeToggle />
		</div>
	</div>
</header>
<main class="container py-8 md:py-12">
	<slot></slot>
</main>

<style></style>
