
<script>
    import ClickOutside from 'svelte-click-outside';
    import Spinner from '$lib/Spinner.svelte'
	import { Tabs, Tab, TabList, TabPanel } from 'svelte-tabs';
	import { slide } from 'svelte/transition';



    let triggerEl;
    let triggerEl2;
    let genrepanelVisible = false;
    let panelVisible = false;

    let searchedMovies;
    let searchedMoviesList = [];
    let searchedActors;
    let searchedActorsList = [];

	let val='';
	let timer;

	const debounce = v => {
		clearTimeout(timer);
		timer = setTimeout(() => {
			if(v && v.trim()){
				val = v;		
			}
		}, 750);
	}

	$: if(panelVisible && val){
		searchedMovies =  getMovies(val);
		searchedActors = getActors(val);
	}



	async function getMovies(term){
		searchedMoviesList = [];
		const res = await fetch(`https://api.themoviedb.org/3/search/movie?api_key=04c35731a5ee918f014970082a0088b1&language=en-US&query=${term}&page=1&include_adult=true`);
		const text = await res.json();
		if (res.ok) {
			searchedMoviesList.push(text.results);
			searchedMoviesList = searchedMoviesList;
		} else {
			throw new Error(text);
		}
	}

	async function getActors(term){
		searchedActorsList = [];
		const res = await fetch(`https://api.themoviedb.org/3/search/person?api_key=04c35731a5ee918f014970082a0088b1&language=en-US&query=${term}&page=1&include_adult=false`);
		const text = await res.json();
		if (res.ok) {
			searchedActorsList.push(text.results);
			searchedActorsList = searchedActorsList;
		} else {
			throw new Error(text);
		}
	}

	async function getGenreList() {
		const res = await fetch(`https://api.themoviedb.org/3/genre/movie/list?api_key=04c35731a5ee918f014970082a0088b1&language=en-US`);
		const text = await res.json();
		if (res.ok) {
			return text;
		} else {
			throw new Error(text);
		}
	}

	let promise = getGenreList();

 
    function genretogglePanel() {
      genrepanelVisible = !genrepanelVisible;
    }
    function toggleSearch() {
    	panelVisible = !panelVisible;
    }
 
    function hidePanel() {
      genrepanelVisible = false;
    }
    function hideSearch() {
      panelVisible = false;
    }

	function formatDate(userDOB) {
	  const dob = new Date(userDOB);

	  const monthNames = [
	    'January', 'February', 'March', 'April', 'May', 'June', 'July',
	     'August', 'September', 'October', 'November', 'December'
	  ];

	  const day = dob.getDate();
	  const monthIndex = dob.getMonth();
	  const year = dob.getFullYear();

	  return `${day} ${monthNames[monthIndex]} ${year}`;
	}
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
<nav>
	<ul>
		<a href="/" class="title">Movie Space</a>
		<li><a class="link" href="/">Home</a></li>
		<li><a sveltekit:prefetch class="link" href="/movie">Now Playing</a></li>
	</ul>

	<ul>
		<li><a class="link" href="/trending">Trending</a></li>
		<li><a class="link" href="/top_rated">Top Rated</a></li>
		<li><a class="link" href="/popular">Popular</a></li>
		<li><a class="link" href="/people">People</a></li>
		<li class="drop-down-btn" bind:this={triggerEl} on:click={genretogglePanel}>
			<span>Genres</span>
			<span>
				<svg xmlns="http://www.w3.org/2000/svg" width="13" height="13" fill="currentColor" viewBox="0 0 24 24"><path d="M0 7.33l2.829-2.83 9.175 9.339 9.167-9.339 2.829 2.83-11.996 12.17z"/></svg>
			</span>		
		</li>
		<li class="search-icon" bind:this={triggerEl2} on:click={toggleSearch}>
			<svg fill="currentColor" xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 512 512"><title>Search</title><path d="M456.69,421.39,362.6,327.3a173.81,173.81,0,0,0,34.84-104.58C397.44,126.38,319.06,48,222.72,48S48,126.38,48,222.72s78.38,174.72,174.72,174.72A173.81,173.81,0,0,0,327.3,362.6l94.09,94.09a25,25,0,0,0,35.3-35.3ZM97.92,222.72a124.8,124.8,0,1,1,124.8,124.8A124.95,124.95,0,0,1,97.92,222.72Z"/></svg>
		</li>	
	</ul>
</nav>

  <ClickOutside on:clickoutside={hidePanel} exclude={[triggerEl]}>
    <div hidden={!genrepanelVisible}>
	    <div class="genre-tab">
	    		{#await promise}
					<Spinner />
				{:then genreList}
					{#each genreList.genres as genre}
						<a on:click={genretogglePanel} href={`/movie/genre/${slugify(genre.name)}_${genre.id}`} class="genre-pill">{genre.name}</a>
					{/each}
				{:catch error}
					<p style="color: red">{error.message}</p>
				{/await}
		</div>
    </div>
  </ClickOutside>


<ClickOutside on:clickoutside={hideSearch} exclude={[triggerEl2]}>
    <div hidden={!panelVisible}>
    	<div class="search-tab">
    		<input on:keyup={({ target: { value } }) => debounce(value)} class="search-input" type="text" placeholder="Search Movie or Actor...">
    			{#if val}
	    			<Tabs>
						  <TabList>
						    <Tab>Movies</Tab>
						    <Tab>Actors</Tab>
						  </TabList>
						 
						  <TabPanel>
							{#each searchedMoviesList as movieList}
			    				{#each movieList as movie}
			    					<a transition:slide sveltekit:prefetch href={`/movie/${slugify(movie.title)}_${movie.id}`} class="sample-movie-card">
			    						{#if movie.poster_path === null}
					  						<img src="https://disc1.filmdownload.stream/assets/general/images/no_poster.jpg" alt="{movie.title}">
										{:else}
											<img src="https://image.tmdb.org/t/p/w500{movie.poster_path}" alt="{movie.title}">
										{/if}
										<div class="meta-data">
												<p class="name">{movie.title}</p>
												<p class="dates">
													<span>
														<svg xmlns="http://www.w3.org/2000/svg" width="9" height="9" fill="currentColor" class="bi bi-calendar2-check" viewBox="0 0 16 16">
														  <path d="M10.854 8.146a.5.5 0 0 1 0 .708l-3 3a.5.5 0 0 1-.708 0l-1.5-1.5a.5.5 0 0 1 .708-.708L7.5 10.793l2.646-2.647a.5.5 0 0 1 .708 0z"/>
														  <path d="M3.5 0a.5.5 0 0 1 .5.5V1h8V.5a.5.5 0 0 1 1 0V1h1a2 2 0 0 1 2 2v11a2 2 0 0 1-2 2H2a2 2 0 0 1-2-2V3a2 2 0 0 1 2-2h1V.5a.5.5 0 0 1 .5-.5zM2 2a1 1 0 0 0-1 1v11a1 1 0 0 0 1 1h12a1 1 0 0 0 1-1V3a1 1 0 0 0-1-1H2z"/>
														  <path d="M2.5 4a.5.5 0 0 1 .5-.5h10a.5.5 0 0 1 .5.5v1a.5.5 0 0 1-.5.5H3a.5.5 0 0 1-.5-.5V4z"/>
														</svg>
														{formatDate(movie.release_date)}
													</span>
													<span>&middot;</span>
													<span>
														<svg xmlns="http://www.w3.org/2000/svg" width="9" height="9" fill="currentColor" class="bi bi-translate" viewBox="0 0 16 16">
														  <path d="M4.545 6.714 4.11 8H3l1.862-5h1.284L8 8H6.833l-.435-1.286H4.545zm1.634-.736L5.5 3.956h-.049l-.679 2.022H6.18z"/>
														  <path d="M0 2a2 2 0 0 1 2-2h7a2 2 0 0 1 2 2v3h3a2 2 0 0 1 2 2v7a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2v-3H2a2 2 0 0 1-2-2V2zm2-1a1 1 0 0 0-1 1v7a1 1 0 0 0 1 1h7a1 1 0 0 0 1-1V2a1 1 0 0 0-1-1H2zm7.138 9.995c.193.301.402.583.63.846-.748.575-1.673 1.001-2.768 1.292.178.217.451.635.555.867 1.125-.359 2.08-.844 2.886-1.494.777.665 1.739 1.165 2.93 1.472.133-.254.414-.673.629-.89-1.125-.253-2.057-.694-2.82-1.284.681-.747 1.222-1.651 1.621-2.757H14V8h-3v1.047h.765c-.318.844-.74 1.546-1.272 2.13a6.066 6.066 0 0 1-.415-.492 1.988 1.988 0 0 1-.94.31z"/>
														</svg>
														{movie.original_language.toUpperCase()}
													</span>
												</p>
										</div>
										<p class="vote">
											<span>
												{movie.vote_average}
											</span>
										</p>
			    					</a>
			    				{/each}
							{/each}
						  </TabPanel>
						 
						  <TabPanel>
							{#each searchedActorsList as actorsList}
			    				{#each actorsList as actor}
			    					<a transition:slide sveltekit:prefetch href={`/people/${slugify(actor.name)}_${actor.id}`} class="sample-movie-card">
			    						{#if actor.profile_path === null}
					  						<img src="https://disc1.filmdownload.stream/assets/general/images/no_poster.jpg" alt="{actor.title}">
										{:else}
											<img src="https://image.tmdb.org/t/p/w500{actor.profile_path}" alt="{actor.title}">
										{/if}
										<div class="meta-data">
												<p class="name">{actor.name}</p>
												<p class="dates">
													<span>
														<svg xmlns="http://www.w3.org/2000/svg" width="9" height="9" fill="currentColor" class="bi bi-translate" viewBox="0 0 16 16">
														  <path d="M4.545 6.714 4.11 8H3l1.862-5h1.284L8 8H6.833l-.435-1.286H4.545zm1.634-.736L5.5 3.956h-.049l-.679 2.022H6.18z"/>
														  <path d="M0 2a2 2 0 0 1 2-2h7a2 2 0 0 1 2 2v3h3a2 2 0 0 1 2 2v7a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2v-3H2a2 2 0 0 1-2-2V2zm2-1a1 1 0 0 0-1 1v7a1 1 0 0 0 1 1h7a1 1 0 0 0 1-1V2a1 1 0 0 0-1-1H2zm7.138 9.995c.193.301.402.583.63.846-.748.575-1.673 1.001-2.768 1.292.178.217.451.635.555.867 1.125-.359 2.08-.844 2.886-1.494.777.665 1.739 1.165 2.93 1.472.133-.254.414-.673.629-.89-1.125-.253-2.057-.694-2.82-1.284.681-.747 1.222-1.651 1.621-2.757H14V8h-3v1.047h.765c-.318.844-.74 1.546-1.272 2.13a6.066 6.066 0 0 1-.415-.492 1.988 1.988 0 0 1-.94.31z"/>
														</svg>
														{actor.known_for_department}
													</span>
												</p>
										</div>
										<p class="vote">
											<span>
												{actor.popularity}
											</span>
										</p>
			    					</a>
			    				{/each}
							{/each}
						  </TabPanel>
					</Tabs>
				{:else}
					<div class="fall-back">
						Waiting for Your Search...!
						<Spinner />
					</div>
				{/if}
    	</div>
    </div>
</ClickOutside>




<style>
	.fall-back{
		display: flex;
		align-items: center;
		justify-content: center;
		flex-direction: column;
		width: 100%;
	}
	:global(.search-tab .svelte-tabs),
	:global(.search-tab .svelte-tabs li.svelte-tabs__selected),
	:global(.search-tab .svelte-tabs li.svelte-tabs__tab),
	:global(.search-tab .svelte-tabs div.svelte-tabs__tab-panel){
		border: 0 !important;
		outline: 0 !important;
		width: 100%;
	}
	:global(.search-tab .svelte-tabs div.svelte-tabs__tab-panel){
		margin-top: 1rem;
		width: 100%;
	}
	:global(.search-tab .svelte-tabs__tab-list){
		display: inline-flex;
		align-items: center;
		justify-content: center;
		border: 0 !important;
		text-align: center !important;
	}
	:global(.search-tab .svelte-tabs li.svelte-tabs__tab){
		text-align: center;
		color: #C84B31;
		font-size: 1rem;
		font-weight: 500;
		text-transform: uppercase;
	}
	:global(.search-tab .svelte-tabs li.svelte-tabs__selected){
		color: #C84B31 !important;
		box-shadow: 0px 3px 0px 0px #C84B31;
		color: black;
	}
	.sample-movie-card img{
		width: 4rem;
	}
	.sample-movie-card{
		margin: 0.2rem 0;
		padding: 0.5rem;
		display: flex;
		width: 100%;
	}
	.sample-movie-card:hover{
		box-shadow: 0px 0px 0px 2px #C84B31;
		/*border: 1px solid #C84B31;*/
	}
	.meta-data{
		padding:0 0.5rem;
		padding-left: 0.7rem;
		display: flex;
		flex: 1;
		align-items: flex-start;
		flex-direction: column;
		line-height: 1.5;
	}
	.meta-data .name{
		line-height: 1.2;
		font-size: 1.1rem;
		color: #C84B31;
	}
	.meta-data .dates{
		opacity: 0.8;
		font-size: 0.8rem;
	}
	.vote{
		display: inline-block;
	}
	.vote span{
		padding: 0 0.4rem;
		font-size: 0.9rem;
		border: 3px;
		background-color: #C84B31;

	}
	::placeholder{
		font-size: 1rem;
	}
	.search-input{
		outline: 0;
		font-family: inherit;
		font-size: 1.1rem;
		padding: 0.2rem 0.5rem;
		border: 1px solid #C84B31;
		width: 100%;
		margin-bottom: 1rem;
	}
	.genre-pill{
		margin-right: 1rem;
		border: 1px solid #C84B31;
		padding: 0.8rem 1.1rem;
	}
	.genre-pill:hover{
		color: white;
		background-color: #C84B31;
	}
	.genre-tab,
	.search-tab{
		z-index: 10;
		right: 4%;
		border-top: 4px solid #C84B31;
		border-bottom: 4px solid #C84B31;
		position: absolute;
		width: 50.5%;
		height: 20rem;
		box-shadow: rgba(0, 0, 0, 0.24) 0px 3px 8px;
		padding: 2rem;
		display: flex;
		align-items: flex-start;
		flex-wrap: wrap;
	}
	.search-tab{
		right: 1%;
		width: 30%;
		height: 85%;
		display: flex;
		overflow-y: auto;
	}
	nav{
		padding: 0 1rem;
		margin: 0 auto;
		margin-bottom: 1rem;
		display: flex;
		align-items: center;
		justify-content: space-between;
		box-shadow: rgba(0, 0, 0, 0.24) 0px 3px 8px;
		width: 100%;
		height: 4rem;
	}
	ul{
		display: flex;
		align-items: center;
	}
	li a{
		font-weight: 500;
		margin-right: 0.6rem;
		padding: 0.8rem 0.7rem;
		color: rgba(255, 255, 255, 0.8);
		transition: color 0.2s eas-in;
	}
	li a::last-child{
		margin-right: 0;
	}
	.search-icon{
		cursor: pointer;
		margin-left: 1rem;
		padding: 0.3rem 0.35rem;
		box-shadow: 0px 0px 0px 2px #C84B31;
	}
	.search-icon svg{
		fill: #C84B31;
	}
	.title{
		margin-right: 1.1rem;
		padding: 0.5rem 0.7rem;
		box-shadow: 0px 0px 0px 2px #C84B31;
		cursor: pointer;
	}
	.underline{
  		position: relative;
	}
	.link:hover{
		color: #C84B31;
	}
	.drop-down-btn{
		display: flex;	
		align-items: center;
		padding: 0.4rem 0.5rem;
		padding-right: 0.1rem;
		box-shadow: 0px 0px 0px 2px #C84B31;
		cursor: pointer;
	}
	.drop-down-btn span{
		margin-right: 0.5rem;
	}
	.drop-down-btn span:hover{
		color: #C84B31;
	}
	.drop-down-btn:hover svg{
		fill: #C84B31;
	}
	.drop-down-btn svg{
		margin-top: 0.18rem;
	}
</style>