<script>
	import Counter from './Counter.svelte';
	import welcome from '$lib/images/svelte-welcome.webp';
	import welcome_fallback from '$lib/images/svelte-welcome.png';
	import { goto } from '$app/navigation';

	let name = "";

	function toNamePage() {
		if (name.length <= 0) {
			return;
		}
		let url = '/tree?name='+name;
		console.log(name);
		console.log(url);
		goto(url);
	}
</script>

<svelte:head>
	<title>Home</title>
	<meta name="description" content="Svelte demo app" />
</svelte:head>

<section>
	<div class="text-column "> 
		<h1>Welcome to Arbol</h1>
		<p>Arbol is community tree app designed to soften our interaction with each other and recontextualize our relationship with online spaces.</p>
		<p>What is your name?</p>
		<input bind:value={name}>
		<br>
		<button class="main-button" on:click={toNamePage}>Go to your page</button>
	</div>

</section>

<style>
	section {
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		flex: 0.6;
	}

	h1 {
		width: 100%;
	}

	.welcome {
		display: block;
		position: relative;
		width: 100%;
		height: 0;
		padding: 0 0 calc(100% * 495 / 2048) 0;
	}

	.welcome img {
		position: absolute;
		width: 100%;
		height: 100%;
		top: 0;
		display: block;
	}
</style>
