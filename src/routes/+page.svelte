<script lang="ts">
	import HoverableCard from '$lib/components/HoverableCard.svelte';
	import { Button } from '$lib/components/ui/button';
	import { buttonVariants } from '$lib/components/ui/button';
	import { cn } from '$lib/utils';
	import Icon from '@iconify/svelte';
	import * as Select from '$lib/components/ui/select';
	import data from '$lib/data/data.json';
	import * as Command from '$lib/components/ui/command/index.js';
	import * as Popover from '$lib/components/ui/popover/index.js';
	import { tick } from 'svelte';
	import { Check } from 'svelte-radix';
	import { ScrollArea } from '$lib/components/ui/scroll-area/index.js';
	import Input from '$lib/components/ui/input/input.svelte';
	import type { FormInputEvent, InputEvents } from '$lib/components/ui/input';

	type Oil = {
		name: string;
		sap: number;
		iodine: number;
		ins: number;
		lauric: number;
		myristic: number;
		palmitic: number;
		stearic: number;
		ricinoleic: number;
		oleic: number;
		linoleic: number;
		linolenic: number;
	};

	let oils: Oil[] = data;

	const KOH = 'KOH';
	const NaOH = 'NaOH';

	type LyeType = typeof KOH | typeof NaOH;

	type Units = 'g' | 'kg' | '℥';

	let currentUnit: Units = 'g';

	let selectedLye: LyeType;

	function SelectLyeType(lye: LyeType) {
		selectedLye = lye;
	}

	type SelectedOil = {
		oil: Oil;
		amountInGrams: number;
	};

	let selectedOils: SelectedOil[] = [];

	let totalBatchInGrams = 0;

	function ClaculateTotalBatch() {
		totalBatchInGrams = selectedOils.reduce((sum, x) => (sum += x.amountInGrams), 0);
	}

	function IsOilSelected(oil: Oil) {
		return !!selectedOils.find((x) => x.oil.name == oil.name);
	}

	function ToggleOil(oil: Oil) {
		const index = selectedOils.findIndex((x) => x.oil == oil);
		if (index >= 0) {
			selectedOils.splice(index, 1);
			selectedOils = selectedOils;
			ClaculateTotalBatch();
			return;
		}

		selectedOils = [...selectedOils, { oil: oil, amountInGrams: 0 }];
	}

	function FormatAmount(amount: number) {
		let result = 0;

		switch (currentUnit) {
			case 'g':
				result = amount;
				break;

			case 'kg':
				result = amount / 1000;
				break;

			case '℥':
				result = amount / 28.34952;
				break;

			default:
				result = amount;
				break;
		}

		if (result % 1 == 0) return result;

		return result.toFixed(2);
	}

	function UpdateAmount(amount: number, selectedOil: SelectedOil) {
		switch (currentUnit) {
			case 'g':
				selectedOil.amountInGrams = amount;
				break;

			case 'kg':
				selectedOil.amountInGrams = amount * 1000;
				break;

			case '℥':
				selectedOil.amountInGrams = amount;
				break;

			default:
				break;
		}
		console.log('yes');

		ClaculateTotalBatch();
	}

	function handleTextChange(selectedOil: SelectedOil, e: FormInputEvent<InputEvent>) {
		const value = Number((e.target as HTMLInputElement).value);
		console.log(value);

		if (isNaN(value)) {
			return;
		}
		UpdateAmount(value, selectedOil);
	}
</script>

<svelte:head>
	<title>Open Soap Calculator</title>
</svelte:head>

<h1 class="mb-4 text-3xl font-bold md:text-4xl">Crafting the Perfect Soap</h1>
<p class="mb-6 text-gray-600">An in depth soap calculator to help you create unique recipes</p>

<div class="space-y-4">
	<div class="">
		<h3 class="scroll-m-20 text-2xl font-semibold tracking-tight">Pick Your Lye</h3>
		<p class="text-sm text-muted-foreground">
			The type of lye you will be using for your crafting, this will the type of your soap! Liquid
			or Solid!
		</p>
	</div>
	<div class="grid gap-4 md:grid-cols-2">
		<HoverableCard
			icon="fa6-solid:soap"
			active={selectedLye == NaOH}
			on:click={(_) => SelectLyeType(NaOH)}
		>
			<span slot="title">NaOH (Sodium Hydroxide)</span>
			<span slot="description">Used for solid soap bars</span>
		</HoverableCard>

		<HoverableCard
			icon="covid:personal-hygiene-hand-liquid-soap"
			active={selectedLye == KOH}
			on:click={(_) => SelectLyeType(KOH)}
		>
			<span slot="title">KOH (Potasium Hydroxide)</span>
			<span slot="description">Used for liquid soap</span>
		</HoverableCard>
	</div>
	<div class="">
		<h3 class="scroll-m-20 text-2xl font-semibold tracking-tight">Add your Oils</h3>
		<p class="text-sm text-muted-foreground">
			The type of lye you will be using for your crafting, this will the type of your soap! Liquid
			or Solid!
		</p>
	</div>
	<div class="space-y-1">
		<h4 class="scroll-m-20 text-xl font-semibold tracking-tight">Total Batch Size</h4>
		<div class="grid flex-wrap items-center gap-2 md:flex">
			<div class="relative">
				<Input class="p-5" value={FormatAmount(totalBatchInGrams)} readonly></Input>
				<span class=" absolute bottom-0 right-3 top-3 text-sm text-muted-foreground">
					{currentUnit}
				</span>
			</div>
			<div class="flex gap-2">
				<Button on:click={() => (currentUnit = 'g')}>g</Button>
				<Button on:click={() => (currentUnit = 'kg')}>kg</Button>
				<Button on:click={() => (currentUnit = '℥')}>ounce</Button>
			</div>
		</div>
	</div>

	<div class="flex grid-cols-[250px_1fr] flex-col gap-4 py-2 md:grid">
		<div class="flex flex-col gap-2">
			<h4 class="scroll-m-20 text-xl font-semibold tracking-tight">Oil List</h4>
			<p class="text-sm text-muted-foreground">Click to select, click again to deselect</p>
			<div class="scrollbar h-[400px] overflow-auto rounded-md border">
				<div class="flex flex-col">
					{#each oils as oil}
						<button
							class={cn(
								buttonVariants({ variant: 'ghost' }),
								IsOilSelected(oil) && 'bg-muted',
								'h-fit justify-between py-4 md:py-2 text-wrap rounded-none text-left [&:not(:last-child)]:border-b-[1px] dark:border-neutral-700'
							)}
							on:click={() => ToggleOil(oil)}
						>
							{oil.name}

							{#if IsOilSelected(oil)}
								<Check class="h-5 w-5" />
							{/if}
						</button>
					{/each}
				</div>
			</div>
		</div>
		<div class="flex max-w-[500px] flex-col gap-2 px-0 md:px-4">
			<h4 class="scroll-m-20 text-xl font-semibold tracking-tight">
				You have <bold>{selectedOils.length}</bold> oils selected
			</h4>
			<p class="text-sm text-muted-foreground">
				Edit the amount of oil to automatically recalculate the final value
			</p>
			<div class="scrollbar h-[400px] overflow-scroll space-y-2 md:pr-4 py-1">
				{#each selectedOils as selectedOil}
					<div class="grid grid-cols-2 gap-2">
						<div>
							{selectedOil.oil.name}
						</div>
						<div class="relative">
							<Input
								class="p-5"
								value={FormatAmount(selectedOil.amountInGrams)}
								on:input={(e) => handleTextChange(selectedOil, e)}
							></Input>
							<span class="absolute bottom-0 right-3 top-3 text-sm text-muted-foreground"
								>{currentUnit}</span
							>
						</div>
					</div>
				{/each}
			</div>
		</div>
	</div>
</div>
