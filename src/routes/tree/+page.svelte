<script>
	import { confetti } from '@neoconfetti/svelte';
	import { enhance } from '$app/forms';
	import Quote from './Quote.svelte';
	import { onMount } from 'svelte';
	import { goto } from '$app/navigation';

	let currentDate = new Date();

	import { reduced_motion } from './reduced-motion';

    import { page } from '$app/stores';

	let recommendedTracks = [];
	let recommendedTrackLink = "";
	let redirectURL;
	let quote = {};
	quote.content = "Never apologize for showing feelings. When you do so, you apologize for the truth.";
	quote.author = "Benjamin Disraeli";
 
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

	// ON MOUNT ****************
	onMount(() => {
		loadQuote();
		runSpotify();
	});

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

	function toYT() {
		// youtube
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
		return mediaurl;
	}

	function toMedia() {
		// decide if youtube or Spotify
		let mediaurl = "";
		if (Math.random() < 0.5) {
			// youtube
			mediaurl = toYT();
		} else {
			// spotify
			mediaurl = recommendedTrackLink;
			if (mediaurl.length <= 0) {
				mediaurl = toYT();
			}
		}
		console.log(mediaurl);
		if (mediaurl.length <= 0) {
			return;
		}
		redirectURL = mediaurl;
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
	spotify_tracks_by_name['juju'] = ['5NLu9XiiF80Txfg2Pl8zE8', '0p6PpE8jYE85OwULXmP3M8'];
	spotify_tracks_by_name['anne'] = ['62IjLAfQtP8c1sLvXY2pic', '5jximgvZO7gGAFQndsSltj', '36jZhZP8B5tD9rOtTEnO1W'];
	spotify_tracks_by_name['daniel'] = ['1oqniN5rzgvhAWoq4HgY2W', '1mea3bSkSGXuIRvnydlB5b'];
	spotify_tracks_by_name['jane'] = ['5KqldkCunQ2rWxruMEtGh0', '36jZhZP8B5tD9rOtTEnO1W'];
	spotify_tracks_by_name['sally'] = ['0LQtEJt7x0s6knb6RKdRYc','3U8dHeggJ8IBe0UCb1gbyB'];
	spotify_tracks_by_name['mike'] = ['70LcF31zb1H0PyJoS1Sx1r','1OvFn7fs7eXGyYVXCypH1P'];
	// spotify_tracks_by_name['peter'] = ['5NLu9XiiF80Txfg2Pl8zE8', ]
	spotify_tracks_by_name['grace'] = ['7uqvt9TCztQgtyQZoQdjrF','4ZtFanR9U6ndgddUvNcjcG','3Z2anmIVG8b1GelyeFQdnP','5F8iUzT8P0OChwjo6GxEkr','3vv9phIu6Y1vX3jcqaGz5Z'];
	spotify_tracks_by_name['default'] = ['5NLu9XiiF80Txfg2Pl8zE8', '1mea3bSkSGXuIRvnydlB5b', '6sIki0jaxEEr5LgAhEugye', '36jZhZP8B5tD9rOtTEnO1W']

	async function getRecommendations(){
		// Endpoint reference : https://developer.spotify.com/documentation/web-api/reference/get-recommendations
		let keyname = name.toLowerCase();
		let topTracksIds = spotify_tracks_by_name[keyname] ? spotify_tracks_by_name[keyname] : spotify_tracks_by_name['default'];
		return (await fetchWebApi(
			`v1/recommendations?limit=5&seed_tracks=${topTracksIds.join(',')}`, 'GET'
		)).tracks;
	}

	async function runSpotify() {
		recommendedTracks = await getRecommendations();
		console.log(recommendedTracks);
		recommendedTrackLink = recommendedTracks[0].external_urls.spotify;
		console.log(
			recommendedTracks.map(
				({name, artists}) =>
				`${name} by ${artists.map(artist => artist.name).join(', ')}`
			)
		);
		toMedia();
	}

	async function loadQuote() {
		console.log("loading quote");

		// if quote already loaded and not requested new, quit
		// if (quote.content && !reload) {
		//     return;
		// }

		let res = await fetch(`https://api.quotable.io/random`);
		let data = await res.json();
		console.log(data);
		

		if (!res.ok || !data) {
			console.error('Failed to fetch quote:', data);
			let data = {'author': 'Unknown', 'content': 'Kindness is the language that the deaf can hear and the blind can see'}
		console.log(data);
		}

		quote.content = data.content;
		quote.author = data.author;
	}

	
</script>
<!-- <svelte:window on:keydown={keydown} /> -->

<svelte:head>
	<title>Arbol</title>
	<meta name="description" content="A flexible community tree" />
</svelte:head>


<div class="text-column">

	<h1 class="visually-hidden">Sverdle</h1>

	<!-- <button on:click={runSpotify}>Spotify</button> -->

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
	
	<section>
		<div><em>{quote.content}</em></div>
		<footer>{quote.author}</footer>
	</section>

	<div>
		<label for="feeling-slider"><h2 style="margin-bottom: 10px">How are you feeling today?</h2></label>
	</div>
	<input type="range" id="feeling-slider" min="0" max="100" value="50">
	{#if redirectURL}
		<a class="main-button" id="recommended-media-button" href={redirectURL} target="_blank">
			Explore
		</a>
	{:else}
		<a class="main-button" id="recommended-media-button">
			Explore
		</a>
	{/if}

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

	section {
        border-left: 2px lightgray solid;
        padding: 0 10px 0 30px;
        margin: 10px 0 30px 0;
    }
    section div {
        font-size: 1.1rem;
    }
    section footer {
        margin-top: 10px;
    }
</style>
