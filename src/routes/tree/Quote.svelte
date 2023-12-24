<script context="module">
    import { onMount } from 'svelte';

    let quote = {};

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

</script>

<section>
    <div><em>{quote.content}</em></div>
    <footer>{quote.author}</footer>
</section>
  
<style>
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