<script>
	import Title from '$lib/Route_title.svelte';
</script>

<div class="container">
	<div class="headings">
		<h1>Watch Your <span class="highlite">Favorite Movies</span> Around The World</h1>
		<h2>Discover Actors/Actresses,Crew Members Many <span class="highlite">More To Explore</span></h2>
	</div>
	<a class="btn" href="/trending">Explore Trending Movies</a>
	<div class="source">
		<h2>Sources</h2>
		<p>Movie Space using <a target="_blank" href="https://www.themoviedb.org/about" class="highlite">TMDB</a> as Movie Database.
			The Movie Database (TMDb) is a community built movie and TV database. Every piece of data has been added by amazing community around the world dating back to 2008.It has around 670,669 movies. 2,146,401 people data and many more amazing stats.Thank you <span class="highlite">themoviedb(TMDB)</span>.
		</p>
	</div>
</div>


<style>
	.container{
		padding: 0 1rem;
		margin: 0 auto;
		text-align: center;
		width: 100%;
		height: 75vh;
	}
	.headings{
		margin-bottom: 7rem;
		margin-top: 2rem;
		line-height: 1.5;
		font-size: 2rem;
	}
	.headings h1{
		font-weight: bold;
	}
	.headings h2{
		font-size: 1.3rem;
		margin: 1rem;
	}
	.highlite{
		font-weight: bold;
		color: #C84B31;
	}
	.source h2{
		color: #C84B31;
		font-weight: bold;
		box-shadow: 0px 3px 0px 0px #C84B31;
		text-align: left;
		font-size: 1.5rem;
		margin: 1rem 0;
	}
	.source{
		text-align: left;
		line-height: 1.5;
		width: 60%;
		margin:8rem auto;
	}
	.btn{
		background-color: #C84B31;
		padding: 1rem;
		font-size: 1.2rem;
		color: white;
		font-weight: 500;
		border-radius: 2px;
		margin-bottom: 10rem;
	}
	.btn:hover{
		background-color: #cd5d45;
	}
</style>