<script context="module">
  export async function load({page,fetch}) {



  const movies_week = await fetch(`https://api.themoviedb.org/3/trending/movie/week?api_key=04c35731a5ee918f014970082a0088b1&page=1`);
	const json_movies_week = await movies_week.json();
	const movies_day = await fetch(`https://api.themoviedb.org/3/trending/movie/day?api_key=04c35731a5ee918f014970082a0088b1&page=1`)
	const json_movies_day = await movies_day.json();

	const person_week = await fetch(`https://api.themoviedb.org/3/trending/person/week?api_key=04c35731a5ee918f014970082a0088b1&page=1`);
	const json_person_week = await person_week.json();
	const person_day = await fetch(`https://api.themoviedb.org/3/trending/person/day?api_key=04c35731a5ee918f014970082a0088b1&page=1`);
	const json_person_day = await person_day.json();
    
    return { 
    	props:{
    		trending_movies_week:json_movies_week.results,
    		trending_movies_week_page:json_movies_week.page,
    		trending_movies_week_total_pages:json_movies_week.total_pages,
    		trending_movies_day:json_movies_day.results,
    		trending_movies_day_page:json_movies_day.page,
    		trending_movies_day_total_pages:json_movies_day.total_pages,

    		trending_person_week:json_person_week.results,
    		trending_person_week_page:json_person_week.page,
    		trending_person_week_total_pages:json_person_week.total_pages,
    		trending_person_day:json_person_day.results,
    		trending_person_day_page:json_person_day.page,
    		trending_person_day_total_pages:json_person_day.total_pages,


    	}
    };
  }
</script>

<script>
	import { slide } from 'svelte/transition';
	import { Tabs, Tab, TabList, TabPanel } from 'svelte-tabs';
	import Title from '$lib/Route_title.svelte';
	import MovieCard from '$lib/Movie_card.svelte';
	import Spinner from '$lib/Spinner.svelte';

	export let trending_movies_week;
	export let trending_movies_week_page;
	export let trending_movies_week_total_pages;
	export let trending_movies_day;
	export let trending_movies_day_page;
	export let trending_movies_day_total_pages;

	export let trending_person_week;
	export let trending_person_week_page;
	export let trending_person_week_total_pages;
	export let trending_person_day;
	export let trending_person_day_page;
	export let trending_person_day_total_pages;
	

  const loadTrendingMovieWeek = async () => {
		trending_movies_week_page += 1;
		const res = await fetch(`https://api.themoviedb.org/3/trending/movie/week?api_key=04c35731a5ee918f014970082a0088b1&page=${trending_movies_week_page}`);
		const raw_movie_data = await res.json();

		trending_movies_week = trending_movies_week.concat(raw_movie_data.results);
  };
  const loadTrendingMovieDay = async () => {
		trending_movies_day_page += 1;
		const res = await fetch(`https://api.themoviedb.org/3/trending/movie/day?api_key=04c35731a5ee918f014970082a0088b1&page=${trending_movies_day_page}`);
		const raw_movie_data = await res.json();

		trending_movies_day = trending_movies_day.concat(raw_movie_data.results);
  };


</script>


<Title>
	Trending
</Title>


<div class="container">


	<div class="cat-tabs">
			<Tabs>
				  <TabList>
				    <Tab>today</Tab>
				    <Tab>this week</Tab>
				  </TabList>

				 
				  <TabPanel>
				  		<ul>
							{#each trending_movies_day as movie}
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
						{#if trending_movies_day_page < trending_movies_day_total_pages}
					  		<button class="more-btn" sveltekit:prefetch on:click={loadTrendingMovieDay}>show me more</button>
						{/if}
				  </TabPanel>
				 
				  <TabPanel>
				  		<ul>
							{#each trending_movies_week as movie}
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
						{#if trending_movies_week_page < trending_movies_week_total_pages}
					  		<button class="more-btn" sveltekit:prefetch on:click={loadTrendingMovieWeek}>show me more</button>
						{/if}
				  </TabPanel>
			</Tabs>
	</div>	







</div>



<style>
	.container{
		padding: 0 1rem;
		margin: 2rem auto;
		width: 100%;
	}
	.select-box{
		border: 0;
		outline: 0;
		background-color: #C84B31;
		padding: 0 0.5rem;
		text-align: center;
		text-align-last: center;
		font-size: 1.8rem;
	}
	.select-box option{
		font-size: 1.1rem;
		text-align: center;
  		text-align-last: center;
	}
	ul{
		display: flex;
		align-items: center;
		justify-content:center;
		flex-wrap: wrap;
		margin-bottom: 1rem;
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
		margin-bottom: 1.5rem;
		cursor: pointer;
		user-select: none;
	}
	.more-btn:hover{
		background-color: #C84B31;
	}







	.cat-tabs{
		margin-top: -1rem;
	}
	:global(.cat-tabs .svelte-tabs),
	:global(.cat-tabs .svelte-tabs li.svelte-tabs__selected),
	:global(.cat-tabs .svelte-tabs li.svelte-tabs__tab),
	:global(.cat-tabs .svelte-tabs div.svelte-tabs__tab-panel){
		border: 0 !important;
		outline: 0 !important;
		border: 2px solid #C84B31;
	}
	:global(.cat-tabs .svelte-tabs__tab-list){
		display: flex;
		align-items: center;
		justify-content: center;
		border: 0;
		margin-top: 3rem;
		margin-bottom: 2rem;
	}
	:global(.cat-tabs .svelte-tabs li.svelte-tabs__tab){
		box-shadow: 0px 0px 0px 2px #C84B31;
		text-align: center;
		color: #C84B31;
		font-size: 1rem;
		font-weight: 500;
		text-transform: uppercase;
	}
	:global(.cat-tabs .svelte-tabs li.svelte-tabs__selected){
		background-color: #C84B31;
		color: black;
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