<script context="module">
  export async function load({page,fetch}) {

  	const genre_id = page.params.genre_id;
  	const genre_slug = page.params.genre_sulg;

    const response = await fetch(`https://api.themoviedb.org/3/discover/movie?api_key=04c35731a5ee918f014970082a0088b1&with_genres=${genre_id}&page=1`);
	
		const movie_data = await response.json();
    
    return { 
    	props:{
    		genre_id,
    		genre_slug,
	      currentPage: movie_data.page,
	      notices: movie_data.results,
	      total: movie_data.total_results,
	      totalPage: movie_data.total_pages,
    	}
    };
  }
</script>
<script>

import { slide } from 'svelte/transition';
import Title from '$lib/Route_title.svelte';
import Spinner from '$lib/Spinner.svelte';
import MovieCard from '$lib/Movie_card.svelte';
	
export let genre_id;
export let genre_slug;
export let currentPage;
export let notices;
export let total;
export let totalPage;


  const load_more = async () => {
    currentPage += 1;

		const res = await fetch(`https://api.themoviedb.org/3/discover/movie?api_key=04c35731a5ee918f014970082a0088b1&with_genres=${genre_id}&page=${currentPage}`);
		const movie_data = await res.json();

	   notices = notices.concat(movie_data.results);
  };

	function getYearfromDate(date) {
	  var d = new Date(date);
	  var n = d.getFullYear();
	  return n;
	}
	function numberWithCommas(number) {
    return number.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
	}
	function slugToText(slug) {
	  var words = slug.split('-');

	  for (var i = 0; i < words.length; i++) {
	    var word = words[i];
	    words[i] = word.charAt(0).toUpperCase() + word.slice(1);
	  }

	  return words.join(' ');
	}

</script>


<div class="conatiner">

	<Title>
		Genre: <span class="page-title">{slugToText(genre_slug)}</span>
	</Title>

	<p class="indi">{notices.length} movies out of {total} movies</p>
	<ul>
		{#each notices as movie}
			<li transition:slide sveltekit:prefetch>
				<MovieCard
					movie_poster="{movie.poster_path}"
					movie_title = '{movie.title}'
					movie_release_date = '{movie.release_date}'
					movie_lang = '{movie.original_language}'
					movie_runtime = '{movie.runtime}'
					movie_summary = "{movie.overview}"
					movie_id = "{movie.id}"
				/>
			</li>
		{:else}
			<Spinner />
		{/each}
	</ul>
	{#if currentPage < totalPage}
  		<button title="Page {currentPage} with {notices.length} movies out of {numberWithCommas(total)} movies,Goto page {currentPage + 1}({totalPage})" class="more-btn" sveltekit:prefetch on:click={load_more}>show me more</button>
	{/if}
</div>





<style>
	.conatiner{
		padding: 0 1rem;
		margin: 0 auto;
	}
	ul{
		display: flex;
		align-items: center;
		justify-content:center;
		flex-wrap: wrap;
		margin-bottom: 2rem;
	}
	li{
		overflow: hidden;
		display: inline-flex;
		align-items: flex-start;
		flex-direction: column;
		padding: 0.3rem;
		margin: 0.5rem;
		transition: 0.1s box-shadow ease-in;
	}
	li:hover{
		box-shadow: 0px 0px 0px 2px #C84B31;
	}
	.more-btn{
		display: flex;
		align-items: center;
		justify-content: center;
		text-align: center;
		border: 1px solid #C84B31;
		outline: none;
		background-color: transparent;
		padding:0.4rem 0.8rem;
		border-radius: 2px;
		font-family: inherit;
		text-transform: capitalize;
		margin: 0.5rem auto;
		cursor: pointer;
		user-select: none;
	}
	.more-btn:hover{
		background-color: #C84B31;
	}
	.indi{
		padding: 0 1rem;
		margin: 0 auto;
		user-select: none;
		text-align: right;
		font-style: oblique;
		opacity: 0.3;
		font-size: 0.8rem;
		margin: 0 3.5rem;
		margin-bottom: 0.4rem;
	}
</style>