<script context="module">
  export async function load({page,fetch}) {

  	const person_id = page.params.person_id;
  	const person_name = page.params.person_slug;


    const person = await fetch(`https://api.themoviedb.org/3/person/${person_id}?api_key=04c35731a5ee918f014970082a0088b1&language=en-US`);
	const json_person = await person.json();

    const images = await fetch(`https://api.themoviedb.org/3/person/${person_id}/images?api_key=04c35731a5ee918f014970082a0088b1`);
	const json_images = await images.json();

	const credits = await fetch(`https://api.themoviedb.org/3/person/${person_id}/movie_credits?api_key=04c35731a5ee918f014970082a0088b1&language=en-US`);
	const json_credits = await credits.json();
    
    return { 
    	props:{
    		person_id,
    		person_name,
    		person_data:json_person,
    		person_images:json_images.profiles,
    		person_cast_credits:json_credits.cast,
    		person_crew_credits:json_credits.crew,
    	}
    };
  }
</script>

<script>
	import { slide } from 'svelte/transition';
	import Title from '$lib/Route_title.svelte';
	import { Tabs, Tab, TabList, TabPanel } from 'svelte-tabs';
	import MovieCard from '$lib/Movie_card.svelte';
	import Spinner from '$lib/Spinner.svelte';




	export let person_id;
	export let person_name;
	export let person_data;
	export let person_images;
	export let person_cast_credits;
	export let person_crew_credits;


	function slugToText(slug) {
	  var words = slug.split('-');
	  for (var i = 0; i < words.length; i++) {
	    var word = words[i];
	    words[i] = word.charAt(0).toUpperCase() + word.slice(1);
	  }
	  return words.join(' ');
	}
	function formateDate(date) {
		let d = new Date(date);
		let ye = new Intl.DateTimeFormat('en', { year: 'numeric' }).format(d);
		let mo = new Intl.DateTimeFormat('en', { month: 'long' }).format(d);
		let da = new Intl.DateTimeFormat('en', { day: '2-digit' }).format(d);
		return `${da} ${mo} ${ye}`
	}
	function dateDiffInYears(dateold, datenew) {
            var ynew = datenew.getFullYear();
            var mnew = datenew.getMonth();
            var dnew = datenew.getDate();
            var yold = dateold.getFullYear();
            var mold = dateold.getMonth();
            var dold = dateold.getDate();
            var diff = ynew - yold;
            if (mold > mnew) diff--;
            else {
                if (mold == mnew) {
                    if (dold > dnew) diff--;
                }
            }
            return diff;
        }
	function getAge(dateString){
	    var today = new Date();
	    var birthDate = new Date(dateString);
	    var age = today.getFullYear() - birthDate.getFullYear();
	    var m = today.getMonth() - birthDate.getMonth();
	    if (m < 0 || (m === 0 && today.getDate() < birthDate.getDate())) 
	    {
	        age--;
	    }
	    return age;
	}
</script>

<Title>
	Person: <span class="page-title">{slugToText(person_name)}</span>
</Title>

<div class="container">
	<div class="bio-data">
		<div class="pic">
			<img src="https://image.tmdb.org/t/p/w500{person_data.profile_path}" class="movie-poster" alt="{person_data.name}">
			<div class="popularity-count">
				<p class="popularity-title">Popularity</p>
				<p class="popularity-stat">{person_data.popularity}</p>
			</div>
		</div>
		<div class="bio-text">
			

		<Tabs>
		  <TabList>
		    <Tab>biography</Tab>
		    <Tab>as Cast</Tab>
		    <Tab>as Crew</Tab>
		    <Tab>Images</Tab>
		  </TabList>
		 
		  <TabPanel>
		    <div transition:slide class="bio-table">
				<p class="person-name"><span class="info-title">Name:</span> {person_data.name}</p>
				<p class="person-birthday"><span class="info-title">Birthday:</span> {formateDate(person_data.birthday)}</p>
				{#if person_data.deathday === null}
					<p class="person-age"><span class="info-title">Age:</span> {getAge(person_data.birthday)} years</p>
				{:else}
					<p class="person-age"><span class="info-title">Age:</span> {dateDiffInYears(new Date(person_data.birthday),new Date(person_data.deathday))} years (Died)</p>
					<p class="person-deathday"><span class="info-title">Deathday:</span> {formateDate(person_data.deathday)}</p>
				{/if}

				<p class="person-department"><span class="info-title">Known for Department:</span> {person_data.known_for_department}</p>
				<p class="person-place"><span class="info-title">Place of Birth:</span> {person_data.place_of_birth}</p>
				<p class="person-imdb"><span class="info-title">IMDB id:</span> <a class="imdb-link" target="-_blank" href="https://www.imdb.com/name/{person_data.imdb_id}/">{person_data.imdb_id}</a></p>
		    	{#if !person_data.homepage === null || !person_data.homepage === ""}
					<p class="person-homepage"><span class="info-title">Website:</span> {person_data.homepage}</p>
				{/if}
		    	<p class="person-bio"><span class="info-title">Biography:</span> {person_data.biography}</p>
		    </div>
		  </TabPanel>
		 
		  <TabPanel>
		  	<ul class="person-movies">
				{#each person_cast_credits as cast}
					<li sveltekit:prefetch transition:slide>
						<MovieCard
							movie_poster="{cast.poster_path}"
							movie_title = '{cast.title}'
							movie_release_date = '{cast.release_date}'
							movie_lang = '{cast.original_language}'
							movie_runtime = '{cast.runtime}'
							movie_summary = "{cast.overview}"
							movie_id = "{cast.id}"
						/>
					</li>
				{:else}
					<Spinner />
				{/each}
			</ul>
		  </TabPanel>
		 
		  <TabPanel>
			<ul class="person-movies">
				{#each person_crew_credits as crew}
					<li sveltekit:prefetch transition:slide>
						<MovieCard
							movie_poster="{crew.poster_path}"
							movie_title = "{crew.title}"
							movie_release_date = '{crew.release_date}'
							movie_lang = '{crew.original_language}'
							movie_runtime = '{crew.runtime}'
							movie_summary = "{crew.overview}"
							movie_id = "{crew.id}"
						/>
					</li>
				{:else}
					<Spinner />
				{/each}
			</ul>
		  </TabPanel>

		  <TabPanel>
		  {#if person_images.length > 0}
		  	<ul class="person-movies">
		  		{#each person_images as image}
			  		<li>
			  			<a href="https://image.tmdb.org/t/p/w500{image.file_path}" target="_blank">
			  				<img class="person-pic" src="https://image.tmdb.org/t/p/w500{image.file_path}" alt="{image.file_path}/{image.aspect_ratio}">
			  			</a>
			  		</li>
		  		{/each}
		  	</ul>
		  {:else}
		  	No Images
		  {/if}
		  </TabPanel>
		</Tabs>














		</div>
	</div>
</div>




<style>
	:global(.page-title){
		font-size: 2rem;
		background-color: #C84B31;
		padding: 0rem 0.7rem;
	}
	.imdb-link:hover{
		color: #C84B31;
	}
	.container{
		padding: 0 1rem;
		margin: 2rem auto;
		width: 90%;
	}
	.bio-data{
		display: flex;
	}
	.pic{
		display: inline-block;
		padding: 0.3rem;
		width: 14rem;
		height: 100%;
		margin-right: 1rem;
		box-shadow: 0px 0px 0px 2px #C84B31;
	}
	.pic img{
		border-radius: 2px;
	}
	.popularity-count{
		text-align: center;
		margin: 0.5rem 0;
	}
	.popularity-title{
		color: #161616;
		padding: 0.2rem 0.5rem;
		margin-top: 0.4rem;
		text-transform: uppercase;
		font-weight: 500;
		font-size: 1.5rem;
		background-color: #C84B31;
		text-align: center;
		display: inline-block;
	}
	.popularity-stat{
		margin-top: 0.6rem;
		font-size: 1.4rem;
	}
	.bio-text{
		width:70%;
	}
	:global(.bio-text .svelte-tabs),
	:global(.bio-text .svelte-tabs li.svelte-tabs__selected),
	:global(.bio-text .svelte-tabs li.svelte-tabs__tab),
	:global(.bio-text .svelte-tabs div.svelte-tabs__tab-panel){
		border: 0 !important;
		outline: 0 !important;
		border: 2px solid #C84B31;
	}
	:global(.bio-text .svelte-tabs__tab-list){
		float: right;
		box-shadow: 0px 0px 0px 2px #C84B31;
		border: 0;
	}
	:global(.bio-text .svelte-tabs li.svelte-tabs__tab){
		text-align: center;
		color: #C84B31;
		font-size: 1rem;
		font-weight: 500;
		text-transform: uppercase;
	}
	:global(.bio-text .svelte-tabs li.svelte-tabs__selected){
		background-color: #C84B31;
		color: black;
	}
	.info-title{
		box-shadow: 0px 2px 0px 0px #C84B31;
		margin-right: 0.4rem;
		color: #C84B31;
	}
	.bio-table{
		line-height: 2.3;
	}
	.bio-table p{
		font-size: 1.1rem;
	}
	input {
	  opacity: 0;
	  position: absolute;
	  pointer-events: none;
	}
	.person-bio{
		line-height: 1.8;
	}
	.person-movies{
		width: 100%;
		display: flex;
		align-items: center;
		justify-content: center;
		flex-wrap: wrap;
		padding-top: 1rem;
	}
	.person-movies li{
		display: inline-flex;
		align-items: flex-start;
		padding: 0.3rem;
		margin: 0.5rem;
		transition: 0.1s box-shadow ease-in;
	}
	.person-movies li:hover{
		box-shadow: 0px 0px 0px 2px #C84B31;
	}
	.person-pic{
		border-radius: 2px;
		width: 11rem;
		height: 14rem;
		object-fit: cover;
	}
</style>