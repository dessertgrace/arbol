<script>
	import { confetti } from '@neoconfetti/svelte';
	import { enhance } from '$app/forms';

	import { reduced_motion } from './reduced-motion';

    import { page } from '$app/stores';
 
    const url = $page.url;
    console.log(url);
    console.log(url.searchParams.get('name')); // John
    let name = url.searchParams.get('name');
    console.log(name)

	export let data;
	export let form;

	/** Whether or not the user has won */
	// $: won = data.answers.at(-1) === 'xxxxx';

	/** The index of the current guess */
	// $: i = won ? -1 : data.answers.length;

	/** Whether the current guess can be submitted */
	// $: submittable = data.guesses[i]?.length === 5;

	/**
	 * A map of classnames for all letters that have been guessed,
	 * used for styling the keyboard
	 */
	let classnames;

	/**
	 * A map of descriptions for all letters that have been guessed,
	 * used for adding text for assistive technology (e.g. screen readers)
	 */
	let description;


	/**
	 * Modify the game state without making a trip to the server,
	 * if client-side JavaScript is enabled
	 */
	function update(event) {
		const guess = data.guesses[i];
		const key = (event.target).getAttribute('data-key');

		if (key === 'backspace') {
			data.guesses[i] = guess.slice(0, -1);
			if (form?.badGuess) form.badGuess = false;
		} else if (guess.length < 5) {
			data.guesses[i] += key;
		}
	}

</script>

<!-- <svelte:window on:keydown={keydown} /> -->

<svelte:head>
	<title>Arbol</title>
	<meta name="description" content="A flexible community tree" />
</svelte:head>

<h1 class="visually-hidden">Sverdle</h1>

{#if name}
	<div
		style="position: absolute; left: 50%; top: 30%"
		use:confetti={{
			particleCount: $reduced_motion ? 0 : undefined,
			force: 0.7,
			stageWidth: window.innerWidth,
			stageHeight: window.innerHeight,
			colors: ['#ff3e00', '#40b3ff', '#676778']
		}}
	>
    </div>
    <div style="position: absolute; left: 50%; top: 30%">
        Hey there {name}
    </div>
{:else}
    <div>
        Hey there! Welcome to Arbol. You'll need an identity soon.
    </div>
{/if}

<style>
	@keyframes wiggle {
		0% {
			transform: translateX(0);
		}
		10% {
			transform: translateX(-2px);
		}
		30% {
			transform: translateX(4px);
		}
		50% {
			transform: translateX(-6px);
		}
		70% {
			transform: translateX(+4px);
		}
		90% {
			transform: translateX(-2px);
		}
		100% {
			transform: translateX(0);
		}
	}
</style>
