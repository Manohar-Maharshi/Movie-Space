<script context="module">
	export async function load({ page, fetch}) {
		const movie_title = page.params.movie_slug;
		const movie_id = page.params.movie_id;

		const res = await fetch(`https://api.themoviedb.org/3/movie/${movie_id}?api_key=04c35731a5ee918f014970082a0088b1&language=en-US`);
		const json = await res.json();


		const imdb_id = json.imdb_id;

		const cast = await fetch(`https://api.themoviedb.org/3/movie/${movie_id}/credits?api_key=04c35731a5ee918f014970082a0088b1&language=en-US`)		
		const json_cast = await cast.json()

		const videos = await fetch(`https://api.themoviedb.org/3/movie/${movie_id}/videos?api_key=04c35731a5ee918f014970082a0088b1&language=en-US`)
		const json_videos = await videos.json()

		const similar = await fetch(`https://api.themoviedb.org/3/movie/${movie_id}/similar?api_key=04c35731a5ee918f014970082a0088b1&language=en-US&page=1`)
		const json_similar = await similar.json()

		const recommendation = await fetch(`https://api.themoviedb.org/3/movie/${movie_id}/recommendations?api_key=04c35731a5ee918f014970082a0088b1&language=en-US&page=1`)
		const json_recommendation = await recommendation.json()
		

		return {
			props: {
				movie_title,
				movie_id,
				imdb_id,
				movie_data:json,
				movie_cast:json_cast,
				movie_videos:json_videos,
				movie_similar:json_similar,
				movie_recommendation: json_recommendation,
			}
		};
	}
	
</script>
<script>
	import Title from "$lib/Route_title.svelte";
	import { Tabs, Tab, TabList, TabPanel } from 'svelte-tabs';
	import MovieCard from '$lib/Movie_card.svelte';
	import Spinner from '$lib/Spinner.svelte';



	export let movie_title;
	export let movie_id;
	export let imdb_id;
	export let movie_data;
	export let movie_cast;
	export let movie_videos;
	export let movie_similar;
	export let movie_recommendation;


	function formatMoney(n) {
	    return "$ " + (Math.round(n * 100) / 100).toLocaleString();
	}
	const convertMinsToHrsMins = (mins) => {
	  let h = Math.floor(mins / 60);
	  let m = mins % 60;
	  h = h < 10 ? '0' + h : h;
	  m = m < 10 ? '0' + m : m;
	  return `${h}:${m}`;
	}
	const formateTime = (n) => `${n / 60 ^ 0} Hours ` + `${n % 60} Minutes`;

	function formatDate(userDOB) {
	  const dob = new Date(userDOB);

	  const monthNames = [
	    'January', 'February', 'March', 'April', 'May', 'June', 'July',
	     'August', 'September', 'October', 'November', 'December'
	  ];

	  const day = dob.getDate();
	  const monthIndex = dob.getMonth();
	  const year = dob.getFullYear();

	  // return day + ' ' + monthNames[monthIndex] + ' ' + year;
	  return `${day} ${monthNames[monthIndex]} ${year}`;
	}


	const formatCount = n => {
	  if (n < 1e3) return n;
	  if (n >= 1e3 && n < 1e6) return +(n / 1e3).toFixed(1) + "K";
	  if (n >= 1e6 && n < 1e9) return +(n / 1e6).toFixed(1) + "M";
	  if (n >= 1e9 && n < 1e12) return +(n / 1e9).toFixed(1) + "B";
	  if (n >= 1e12) return +(n / 1e12).toFixed(1) + "T";
	};
	 function slugify(string) {
		return string
			.toString()
			.trim()
			.toLowerCase()
			.replace(/\s+/g, "-")
			.replace(/[^\w\-]+/g, "")
			.replace(/\-\-+/g, "-")
			.replace(/^-+/, "")
			.replace(/-+$/, "");
	}
</script>

<Title>
	{movie_data.title}
</Title>

<div class="movie-container">
	<div class="video-sources">
		<Tabs>
			  <TabPanel>
			    	<div class="movie-player">
			    		{#if movie_videos.results === null || movie_videos.results === undefined || movie_videos.results === "" || movie_videos.results.length === 0}
			    			<p>No Trailer Found</p>
						{:else}
							<iframe src="https://www.youtube-nocookie.com/embed/{movie_videos.results[0].key}" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
						{/if}
					</div>
			  </TabPanel>
			 
			  <TabPanel>
			    	<div class="movie-player">
						<iframe width="100%" height="100%" class="metaframe" src="https://www.2embed.ru/embed/tmdb/movie?id={movie_id}" frameborder="0" scrolling="no" allow="autoplay; encrypted-media" allowfullscreen="allowfullscreen" webkitallowfullscreen="true" mozallowfullscreen="true"></iframe>
					</div>
			  </TabPanel>
			 
			  <TabPanel>
			    	<div class="movie-player">
						<iframe  class="metaframe" src="https://embedforfree.co/imdb?id={imdb_id}" frameborder="0" scrolling="no" allow="autoplay; encrypted-media" allowfullscreen="allowfullscreen" webkitallowfullscreen="true" mozallowfullscreen="true"></iframe>
					</div>
			  </TabPanel>

  			  <TabPanel>
			    	<div class="movie-player">
						<iframe width="100%" height="100%" class="metaframe" src="https://databasegdriveplayer.co/player.php?imdb={imdb_id}" frameborder="0" scrolling="no" allow="autoplay; encrypted-media" allowfullscreen="allowfullscreen" webkitallowfullscreen="true" mozallowfullscreen="true"></iframe>
					</div>
			  </TabPanel>
			 
			  <TabPanel>
			    	<div class="movie-player">
						<iframe width="100%" height="100%" class="metaframe" src="https://v2.vidsrc.me/embed/{imdb_id}" frameborder="0" scrolling="no" allow="autoplay; encrypted-media" allowfullscreen="allowfullscreen" webkitallowfullscreen="true" mozallowfullscreen="true"></iframe>
					</div>
			  </TabPanel>
			 

			  <TabList>
			    <Tab>Youtube - Trailer</Tab>
			    <Tab>2embed</Tab>
			    <Tab>embedforfree</Tab>
			    <Tab>databasegdriveplayer</Tab>
			    <Tab>vidsrc</Tab>
			  </TabList>
		</Tabs>
	</div>
</div>
	<div class="full-info">
		<div class="movie-info">
			<div class="movie-credits">
				<img src="https://image.tmdb.org/t/p/w500{movie_data.poster_path}" class="movie-poster" alt="{movie_data.original_title}">
				<div class="title">
					<p class="movie-name">{movie_data.title}</p>
					<div class="meta-data">
						{#if movie_data.tagline}
							<p class="movie-tagline"><span class="info-title">Tagline:</span> {movie_data.tagline}</p>
						{/if}
						<p class="movie-lang"><span class="info-title">Original Language:</span> {movie_data.original_language.toUpperCase()}</p>
						<p class="movie-runtime"><span class="info-title">Runtime:</span> {formateTime(movie_data.runtime) }</p>
						<p class="movie-org-title"><span class="info-title">Original Title:</span> {movie_data.original_title}</p>
						<p class="movie-date"><span class="info-title">Release Date:</span> {formatDate(movie_data.release_date)}</p>
						<p class="movie-budget"><span class="info-title">Budget:</span> {formatMoney(movie_data.budget)}</p>
						<p class="movie-revenue"><span class="info-title">Revenue:</span> {formatMoney(movie_data.revenue)}</p>
						<p class="movie-production"><span class="info-title">Production Company:</span>{movie_data.production_companies[0].name}({movie_data.production_companies[0].origin_country})</p>
						
					</div>
				</div>
			</div>
			<div class="genres-containr">
				<div class="genre-list">
					<span class="genre-title">genre:</span>
					{#each movie_data.genres as  genre}
						<a sveltekit:prefetch href={`/movie/genre/${slugify(genre.name)}_${genre.id}`} class="act-genre">{genre.name}</a>
					{/each}
				</div>
				<div class="rating">
					<div class="rate">
						<span class="genre-title rating-title">vote avg:</span>
						{movie_data.vote_average}
					</div>
					<div class="count">
						<span class="genre-title count-title">vote Count:</span>
						{formatCount(movie_data.vote_count)}
					</div>
				</div>
			</div>
			<div class="movie-summary">
				<div class="view">
					<h2 class="summary-title">Overview:</h2>
					<p class="movie-overview">{movie_data.overview}</p>
				</div>
			</div>
		</div>
		<div class="movie-cast">
			<Tabs class="cast-tabs">
				<TabList>
					<Tab class="movie-cast-title">Cast</Tab>
					<span class="and">&</span>
					<Tab class="movie-cast-title">Crew</Tab>
				</TabList>
				<TabPanel>
					{#each movie_cast.cast as cast}
						<a sveltekit:prefetch href={`/people/${slugify(cast.name)}_${cast.id}`} class="cast-card">
							
							{#if cast.profile_path === null}
								<img src="https://www.blogsaays.com/wp-content/uploads/2014/02/no-user-profile-picture-whatsapp.jpg" alt="{movie_title}">
							{:else}
								<img src="https://image.tmdb.org/t/p/w500{cast.profile_path}" alt="{cast.name}">
							{/if}

							<div class="cast-info">
								<p class="cast-name">{cast.name}</p>
								<p class="cast-char-name">({cast.character})</p>
							</div>
						</a>
					{/each}
				</TabPanel>
				<TabPanel>
					{#each movie_cast.crew as crew}
						<a sveltekit:prefetch href={`/people/${slugify(crew.name)}_${crew.id}`} class="cast-card">
							
							{#if crew.profile_path === null}
								<img src="https://www.blogsaays.com/wp-content/uploads/2014/02/no-user-profile-picture-whatsapp.jpg" alt="{movie_title}">
							{:else}
								<img src="https://image.tmdb.org/t/p/w500{crew.profile_path}" alt="{crew.name}">
							{/if}


							<div class="cast-info">
								<p class="cast-name">{crew.name}</p>
								<p class="cast-char-name">({crew.job})</p>
							</div>
						</a>
					{/each}
				</TabPanel>
			</Tabs>
		</div>
	</div>
	<div class="extra-data">
		<div class="similar-movies">
				<p class="summary-title">similar movies:</p>
				<ul>

					{#each movie_similar.results as movie}
						<li sveltekit:prefetch>
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
		</div>
		<div class="recommended-movies">
			<p class="summary-title">recommended movies:</p>
				<ul>

					{#each movie_recommendation.results as movie}
						<li sveltekit:prefetch>
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
		</div>
	</div>




<style>
	.movie-container{
		margin: 1rem auto;
		margin-top: -0.2rem;
		display: flex;
		align-items: center;
		justify-content: center;
		flex-direction: column;
	}
	.movie-player{
		width: 100%;
		height: 33rem;
		background-color: black;
		display: flex;
		align-items: center;
		justify-content: center;
		flex-direction: column;
	}
	iframe{
		width: 100%;
		height: 100%;
	}
	.video-sources{
		width: 100%;
		margin-bottom: -0.5rem;
	}
	:global(.video-sources .svelte-tabs),
	:global(.video-sources .svelte-tabs li.svelte-tabs__selected),
	:global(.video-sources .svelte-tabs li.svelte-tabs__tab),
	:global(.video-sources .svelte-tabs div.svelte-tabs__tab-panel){
		border: 0 !important;
		outline: 0 !important;
		border: 2px solid #C84B31;
	}
	:global(.video-sources .svelte-tabs__tab-list){
		display: inline-flex;
		align-items: center;
		justify-content: center;
		margin:0 17rem;
		margin-left: 18rem;
		margin-top: 1rem;
		box-shadow: 0px 0px 0px 2px #C84B31;
		padding:1rem 2rem;
		border: 0 !important;
		text-align: center !important;
	}
	:global(.video-sources .svelte-tabs li.svelte-tabs__tab){
		text-align: center;
		color: #C84B31;
		font-size: 1rem;
		font-weight: 500;
		text-transform: uppercase;
		padding: 0.5rem 0.8rem;
	}
	:global(.video-sources .svelte-tabs li.svelte-tabs__selected){
		background-color: #C84B31;
		color: black;
	}







	.full-info{
		display: flex;
		align-items: flex-start;
		justify-content: space-between;
	}
	.movie-info{
		padding: 0 1rem;
		margin: 1rem auto;
		width: 70%;
		display: flex;
		justify-content:center;
		flex-direction: column;
	}
	.movie-poster{
		width: 13rem;
	}
	.title{
		margin-left: 1rem;
		display: flex;
		flex-direction: column;
	}
	.info-title{
		box-shadow: 0px 2px 0px 0px #C84B31;
		font-size: 0.9rem;
		margin-right: 0.5rem;
		color: #C84B31;
	}
	.meta-data{
		line-height: 2.3;
		margin-top: 0.3rem;
	}
	.meta-data p{
		font-size: 1rem;
		display: flex;
		align-items: center;
	}
	.movie-name{
		font-size: 1.8rem;
		font-weight: 500;
		color: #C84B31;
	}
	.movie-cast{
		padding: 0 1rem;
		padding: 0.4rem;
		margin: 1rem auto;
		margin-right: 1rem;
		box-shadow: 0px 0px 0px 2px #C84B31;
		width: 30%;
		height: 100%;
	}
	.movie-credits{
		box-shadow: 0px 0px 0px 2px #C84B31;
		padding: 0.4rem;
		display: flex;
		align-items: center;
		justify-content: flex-start;
	}
	:global(.movie-cast .svelte-tabs),
	:global(.movie-cast .svelte-tabs li.svelte-tabs__selected),
	:global(.movie-cast .svelte-tabs li.svelte-tabs__tab),
	:global(.movie-cast .svelte-tabs div.svelte-tabs__tab-panel){
		border: 0 !important;
		outline: 0 !important;
		border: 2px solid #C84B31;
	}
	:global(.movie-cast .svelte-tabs__tab-list){
		border: 0 !important;
		text-align: center !important;
	}
	:global(.movie-cast .svelte-tabs li.svelte-tabs__tab){
		text-align: center;
		font-size: 1.5rem;
		color: #C84B31;
		font-weight: 500;
		text-transform: uppercase;
		padding: 0.8rem 0.4rem;
		padding-bottom: 0.2rem;
		margin-bottom: 0.7rem;
	}
	.movie-cast .and{
		font-size: 1.5rem;
		color: #C84B31;
		font-weight: 500;
	}
	:global(.movie-cast .svelte-tabs li.svelte-tabs__selected){
		box-shadow: 0px 2px 0px 0px #C84B31;
		color: #C84B31;
	}














	.genres-containr{
		margin-top: 1rem;
		display: flex;
		align-items: center;
		justify-content: space-between;
	}
	.genre-list{
		box-shadow: 0px 0px 0px 2px #C84B31;
		height: 3rem;
		width: 100%;
		padding: 0 1rem;
		overflow-x: auto;
		text-overflow: ellipsis;
		white-space: wrap;
		display: flex;
		align-items: center;
		margin-right: 1rem;
	}
	.genre-title{
		text-transform: uppercase;
		display: inline-block;
		padding-bottom: 0.3rem;
		margin-right: 1rem;
		color: #C84B31;
		font-size: 1.2rem;
		font-weight: 500;
		box-shadow: 0px 2px 0px 0px #C84B31;
	}
	.act-genre{
		margin-right: 1rem;
		cursor: pointer;
		white-space: nowrap;
	}
	.act-genre:hover{
		color: #C84B31;
	}
	.rating{
		width: 60%;
		height: 3rem;
		box-shadow: 0px 0px 0px 2px #C84B31;
		margin: auto 0;
		display: flex;
		align-items:center;
		padding: 0 1rem;
	}
	.rating-title{
		font-size: 1rem;
	}
	.count-title{
		font-size: 1rem;
	}
	.rate{
		display: flex;
		align-items: center;
		margin-right: 1rem;
	}
	.count{
		display: flex;
		align-items: center;
	}






	.movie-summary{
		box-shadow: 0px 0px 0px 2px #C84B31;
		margin: 1rem 0;
		overflow: hidden;
		height: 13rem;
		width: 100%;
		text-overflow: ellipsis;
		overflow: auto;
		white-space: wrap;
	}
	.view{
		padding: 1rem;
	}
	.summary-title{
		text-transform: uppercase;
		display: inline-block;
		padding-bottom: 0.3rem;
		margin-bottom: 0.2rem;
		color: #C84B31;
		font-size: 1.2rem;
		font-weight: 500;
		box-shadow: 0px 2px 0px 0px #C84B31;
	}
	.movie-overview{
		line-height: 1.5;
		padding: 0.5rem 0;
	}



	.movie-cast{
		padding: 0 0.5rem;
		height: 38.83rem;
		overflow: auto;
	}
	.movie-cast-title{
		text-align: center;
		padding: 0.5rem 0;
		font-size: 1.5rem;
		color: #C84B31;
		font-weight: 500;
		text-transform: uppercase;
	}
	.cast-card{
		margin-bottom: 0.5rem;
		padding: 0.2rem 0;
		display: flex;
		align-items: center;
		box-shadow: 0px 0px 0px 2px #C84B31;
	}
	.cast-card:hover{
		background-color: #C84B31;
	}
	.cast-card:hover .cast-name,
	.cast-card:hover .cast-char-name{
		background-color: #C84B31;
		color: black;
	}
	.cast-card:hover img{
		box-shadow: 0px 0px 0px 4px #cd5d45;
	}
	.cast-name{
		width: 100%;
		font-size: 1.5rem;
		color: #C84B31;
	}
	.cast-char-name{
		width: 100%;
		font-style: oblique;
		font-size: 0.8rem;
		background-color: inherit;
	}
	.cast-info{
		margin: 0 auto;
		line-height: 1.3;
		text-align: center;
		display: flex;
		align-items: center;
		justify-content: space-between;
		flex-direction: column;
	}
	.cast-card img{
		margin-left: 2rem;
		width: 5rem;
		height: 5rem;
		object-fit: cover;
		border-radius: 50%;
	}





	.extra-data{
		padding: 0 1rem;
		margin: 0 auto;
		margin-top: -0.9rem;
		margin-bottom: 1rem;
		width: 100%;
	}
	ul{
		width: 100%;
		overflow-x: auto;
		display: flex;
		align-items: center;
		margin-left: -0.3rem;
	}
	li{
		display: inline-flex;
		align-items: flex-start;
		padding: 0.3rem;
		margin: 0.5rem;
		transition: 0.1s box-shadow ease-in;
	}
	li:hover{
		box-shadow: 0px 0px 0px 2px #C84B31;
	}
	.similar-movies{
		margin-bottom: 1rem;
	}
	.similar-movies,
	.recommended-movies{
		padding: 1rem;
		width:100%;
		box-shadow: 0px 0px 0px 2px #C84B31;
	}
	ul::-webkit-scrollbar,
	.genre-list::-webkit-scrollbar {
	    width: 5px;
	    height: 10px;
	}

	ul::-webkit-scrollbar-track,
	.genre-list::-webkit-scrollbar-track {
	    background-color: inherit;
	}
	ul::-webkit-scrollbar-thumb:horizontal,
	.genre-list::-webkit-scrollbar-thumb:horizontal{
	    box-shadow: inset 0 0 2px rgba(0, 0, 0, 0.3);
		background-color: #C84B31;
		height: 10px;
        border-radius: 10px;
    }
    .genre-list{
    	overflow-x: auto;
    }
    .act-genre{
    	font-size: 0.97rem;
    }
    .genre-list::-webkit-scrollbar {
	    width: 5px;
	    height: 5px;
	}




	::-webkit-scrollbar {
	    width: 8px;
	}

	::-webkit-scrollbar-track {

	    background-color: inherit;
	}

	::-webkit-scrollbar-thumb {
		border-radius: 5px;
		background-color: #C84B31;
	    box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.3);
	}
</style>