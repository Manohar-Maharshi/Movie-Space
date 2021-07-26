<script>
	export let movie_poster = '';
	export let movie_title = '';
	export let movie_release_date = '';
	export let movie_lang = '';
	export let movie_runtime = '';
	export let movie_summary = "summary";
	export let movie_id = "12345";

	function getYearfromDate(date) {
	  var d = new Date(date);
	  var n = d.getFullYear();
	  return n;
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

	  // return day + ' ' + monthNames[monthIndex] + ' ' + year;
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
<a sveltekit:prefetch title="{movie_title}:(Summary){movie_summary}" href={`/movie/${slugify(movie_title)}_${movie_id}`} class="card">
	


	{#if movie_poster === null}
		<img src="https://disc1.filmdownload.stream/assets/general/images/no_poster.jpg" alt="{movie_title}">
	{:else}
		<img src="https://image.tmdb.org/t/p/w500{movie_poster}" alt="{movie_title}">
	{/if}



	<div class="meta-data">
		<p class="name">{movie_title}</p>
		<p class="dates">
			<span>
				<svg xmlns="http://www.w3.org/2000/svg" width="9" height="9" fill="currentColor" class="bi bi-calendar2-check" viewBox="0 0 16 16">
				  <path d="M10.854 8.146a.5.5 0 0 1 0 .708l-3 3a.5.5 0 0 1-.708 0l-1.5-1.5a.5.5 0 0 1 .708-.708L7.5 10.793l2.646-2.647a.5.5 0 0 1 .708 0z"/>
				  <path d="M3.5 0a.5.5 0 0 1 .5.5V1h8V.5a.5.5 0 0 1 1 0V1h1a2 2 0 0 1 2 2v11a2 2 0 0 1-2 2H2a2 2 0 0 1-2-2V3a2 2 0 0 1 2-2h1V.5a.5.5 0 0 1 .5-.5zM2 2a1 1 0 0 0-1 1v11a1 1 0 0 0 1 1h12a1 1 0 0 0 1-1V3a1 1 0 0 0-1-1H2z"/>
				  <path d="M2.5 4a.5.5 0 0 1 .5-.5h10a.5.5 0 0 1 .5.5v1a.5.5 0 0 1-.5.5H3a.5.5 0 0 1-.5-.5V4z"/>
				</svg>
				{formatDate(movie_release_date)}
			</span>
			<span>&middot;</span>
			<span>
				<svg xmlns="http://www.w3.org/2000/svg" width="9" height="9" fill="currentColor" class="bi bi-translate" viewBox="0 0 16 16">
				  <path d="M4.545 6.714 4.11 8H3l1.862-5h1.284L8 8H6.833l-.435-1.286H4.545zm1.634-.736L5.5 3.956h-.049l-.679 2.022H6.18z"/>
				  <path d="M0 2a2 2 0 0 1 2-2h7a2 2 0 0 1 2 2v3h3a2 2 0 0 1 2 2v7a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2v-3H2a2 2 0 0 1-2-2V2zm2-1a1 1 0 0 0-1 1v7a1 1 0 0 0 1 1h7a1 1 0 0 0 1-1V2a1 1 0 0 0-1-1H2zm7.138 9.995c.193.301.402.583.63.846-.748.575-1.673 1.001-2.768 1.292.178.217.451.635.555.867 1.125-.359 2.08-.844 2.886-1.494.777.665 1.739 1.165 2.93 1.472.133-.254.414-.673.629-.89-1.125-.253-2.057-.694-2.82-1.284.681-.747 1.222-1.651 1.621-2.757H14V8h-3v1.047h.765c-.318.844-.74 1.546-1.272 2.13a6.066 6.066 0 0 1-.415-.492 1.988 1.988 0 0 1-.94.31z"/>
				</svg>
				{movie_lang.toUpperCase()}
			</span>
			{#if movie_runtime}
				<span>&middot;</span>
					<span>
						<svg xmlns="http://www.w3.org/2000/svg" width="9" height="9" fill="currentColor" class="bi bi-hourglass-split" viewBox="0 0 16 16">
								<path d="M2.5 15a.5.5 0 1 1 0-1h1v-1a4.5 4.5 0 0 1 2.557-4.06c.29-.139.443-.377.443-.59v-.7c0-.213-.154-.451-.443-.59A4.5 4.5 0 0 1 3.5 3V2h-1a.5.5 0 0 1 0-1h11a.5.5 0 0 1 0 1h-1v1a4.5 4.5 0 0 1-2.557 4.06c-.29.139-.443.377-.443.59v.7c0 .213.154.451.443.59A4.5 4.5 0 0 1 12.5 13v1h1a.5.5 0 0 1 0 1h-11zm2-13v1c0 .537.12 1.045.337 1.5h6.326c.216-.455.337-.963.337-1.5V2h-7zm3 6.35c0 .701-.478 1.236-1.011 1.492A3.5 3.5 0 0 0 4.5 13s.866-1.299 3-1.48V8.35zm1 0v3.17c2.134.181 3 1.48 3 1.48a3.5 3.5 0 0 0-1.989-3.158C8.978 9.586 8.5 9.052 8.5 8.351z"/>
						</svg>
						{movie_runtime} Min
				</span>
			{/if}
		</p>
	</div>
</a>


<style>
	.card{
		width: 11rem;
	}
	.card img{
		border-radius: 2px;
		width: 11rem;
		height: 14rem;
		object-fit: cover;
	}
	.meta-data{
		margin-top: 0.4rem;
		padding: 0 0.1rem;
	}
	.name{
		width: inherit;
		white-space: nowrap;
		overflow: hidden;
		text-overflow: ellipsis;
		font-weight: bold;
	}
	.name:hover{
		color: #C84B31;
	}
	.dates{
		margin-top: 0.04rem;
	}
	.dates span svg{
		margin-bottom: 0.1rem;
		margin-right: 0.2rem;
	}
	.dates span{
		display: inline-flex;
		align-items: center;
		font-size: 0.7rem;
		font-weight: 500;
		opacity: 0.8;
	}
</style>