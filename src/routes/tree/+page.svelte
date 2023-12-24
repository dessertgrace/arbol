<script>
	import { confetti } from '@neoconfetti/svelte';
	import { enhance } from '$app/forms';
	import Quote from './Quote.svelte';
	import { onMount } from 'svelte';
	import { goto } from '$app/navigation';

	let currentDate = new Date();

	import { reduced_motion } from './reduced-motion';

    import { page } from '$app/stores';
 
    const url = $page.url;
    console.log(url);
    console.log(url.searchParams.get('name')); // John
    let name = url.searchParams.get('name');
	let person = url.searchParams.get('person');
	if (!name || name.length==0) {
		name = person;
	}
	// split and capitalize first letter
	let splitname = name.split(" ");
	console.log(splitname);
	let formatname = "";
	splitname.forEach((word) => {
		formatname = formatname + " " + word.charAt(0).toUpperCase() + word.slice(1)
	});
	name = formatname.trim();
	console.log(name=="Auntjane");
	// get format name
	if (name == 'Auntsally') {
		name = "Sally";
	} else if (name == "Auntjane") {
		name = "Jane";
	}

	let now = new Date();
	const options = {
		year: 'numeric',
		month: 'long',
		day: 'numeric',
	};
	let dateString = now.toLocaleDateString(undefined, options)

	let greetings = ["Good Morning", "Good Afternoon", "Good Evening"];
	let currentTimeSection = 0;
	console.log(now);
	console.log(now.getHours());
	if (now.getHours() >= 17){
		currentTimeSection = 2;
	} else if (now.getHours() >= 12 ) {
		currentTimeSection = 1;
	}

	// PRESELECTED MEDIA ***************
	let people_preselected_urls = {};
	people_preselected_urls['jane'] = [
		"https://www.youtube.com/watch?v=6f0T6UV-HiI",
		"https://www.youtube.com/watch?v=30b7_S0paCQ",
		'backup'
	];
	people_preselected_urls['backup'] = [
		"https://www.youtube.com/watch?v=6f0T6UV-HiI",
		"https://www.youtube.com/watch?v=30b7_S0paCQ",
		"https://youtube.com/shorts/B60Li1SO9QY?si=bHspPNuJClz5NSiI",
		"https://youtu.be/oV8vtpepyGY?si=WVsP3IYU-KVdl2KA",
		"https://youtu.be/avjI3_GIZBw?si=gN5jfPWXBe4N-SE2",
	];
	people_preselected_urls['sally'] = [
		'https://www.youtube.com/watch?v=EtkTtZpsCCY',
		'https://www.youtube.com/watch?v=oouFE51HcqM',
		'backup'
	];
	people_preselected_urls['juju'] = [

	];

	function toMedia() {
		let optionsMedia = people_preselected_urls[name.toLowerCase()];
		if (!optionsMedia || optionsMedia.length == 0) {
			optionsMedia = people_preselected_urls['backup'];
		}
		console.log(optionsMedia);
		let mediaurl = optionsMedia[Math.floor(Math.random()*optionsMedia.length)];
		console.log(mediaurl);
		if (mediaurl == 'backup') {
			optionsMedia = people_preselected_urls['backup'];
			mediaurl = optionsMedia[Math.floor(Math.random()*optionsMedia.length)];
		}
		if (mediaurl.length <= 0) {
			return;
		}
		console.log(mediaurl);
		goto(mediaurl);
	}

	// SPOTIFY 
	const token = 'BQDVTRqE5skUbwi6ehJFsjvJ4wzlHIgwKyBpLp7KEWRm8MT4ugA6vyU9Xys8ehEzVc4ILbtAFswBEFBf7Lhka2zR0anN3NGpTsCAY4axLeqnmqxc6N0Wh07Kr-nZNHyojDyt9FldNNcbvobaFYiEHf2HZ4N0YHhYaStbYqe1NGv4Y4WxBV6akuql3OxdrAOSo9LJVx62Ld_89f7r5IOAchWkFyNgCP9m3Cn1lGIr0M-aVWK-Xw8Iu_EQwgHQN5DcNvhljok';
	async function fetchWebApi(endpoint, method, body) {
		const res = await fetch(`https://api.spotify.com/${endpoint}`, {
			headers: {
				Authorization: `Bearer ${token}`,
				},
				method,
				body:JSON.stringify(body)
			});
		return await res.json();
	}
	
	const topTracksIds = [
		'7uqvt9TCztQgtyQZoQdjrF','4ZtFanR9U6ndgddUvNcjcG','3Z2anmIVG8b1GelyeFQdnP','5F8iUzT8P0OChwjo6GxEkr','3vv9phIu6Y1vX3jcqaGz5Z'
	];
	let spotify_tracks_by_name = {};
	spotify_tracks_by_name['juju'] = ['5NLu9XiiF80Txfg2Pl8zE8', ]
	spotify_tracks_by_name['anne'] = ['5NLu9XiiF80Txfg2Pl8zE8', ]
	spotify_tracks_by_name['daniel'] = ['5NLu9XiiF80Txfg2Pl8zE8', ]
	spotify_tracks_by_name['jane'] = ['5NLu9XiiF80Txfg2Pl8zE8', ]
	spotify_tracks_by_name['sally'] = ['5NLu9XiiF80Txfg2Pl8zE8', ]
	spotify_tracks_by_name['peter'] = ['5NLu9XiiF80Txfg2Pl8zE8', ]
	spotify_tracks_by_name['grace'] = ['7uqvt9TCztQgtyQZoQdjrF','4ZtFanR9U6ndgddUvNcjcG','3Z2anmIVG8b1GelyeFQdnP','5F8iUzT8P0OChwjo6GxEkr','3vv9phIu6Y1vX3jcqaGz5Z'];
	spotify_tracks_by_name['default'] = ['5NLu9XiiF80Txfg2Pl8zE8', ]

	async function getRecommendations(){
		// Endpoint reference : https://developer.spotify.com/documentation/web-api/reference/get-recommendations
		let keyname = name.toLowerCase();
		let topTracksIds = spotify_tracks_by_name[keyname] ? spotify_tracks_by_name[keyname] : spotify_tracks_by_name['default'];
		return (await fetchWebApi(
			`v1/recommendations?limit=5&seed_tracks=${topTracksIds.join(',')}`, 'GET'
		)).tracks;
	}

	async function runSpotify() {
		let recommendedTracks = await getRecommendations();
		console.log(
			recommendedTracks.map(
				({name, artists}) =>
				`${name} by ${artists.map(artist => artist.name).join(', ')}`
			)
		);
	}

	
</script>
<!-- <svelte:window on:keydown={keydown} /> -->

<svelte:head>
	<title>Arbol</title>
	<meta name="description" content="A flexible community tree" />
</svelte:head>


<div class="text-column">

	<h1 class="visually-hidden">Sverdle</h1>

	<button on:click={runSpotify}>Spotify</button>

	{#if name}
		<div
			style="position: absolute; left: 50%; top: -20%"
			use:confetti={{
				particleCount: $reduced_motion ? 0 : 70,
				force: 0.01,
				stageWidth: window.innerWidth,
				stageHeight: window.innerHeight*1.2,
				colors: ['#355c7d', '#c06c84', '#f67280']
			}}
		>
		</div>
		<h1>{greetings[currentTimeSection]}, {name}</h1>
	{:else}
		<h1>{greetings[currentTimeSection]}</h1>
		<p> Welcome to Arbol. You'll need an identity soon.</p>
	{/if}

	<h2>{dateString}</h2>
	<Quote />
	<div>
		<label for="feeling-slider"><h2 style="margin-bottom: 10px">How are you feeling today?</h2></label>
	</div>
	<input type="range" id="feeling-slider" min="0" max="100" value="50">
	<button class="main-button" id="recommended-media-button" on:click={toMedia}>Explore</button>

	<div class="category-buttons">
		<button class="category-button">laughter</button>
		<button class="category-button">beauty</button>
		<button class="category-button">care</button>
		<button class="category-button">thought</button>
	</div>

</div>

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
