<script>
	export let image = "https://disc1.filmdownload.stream/assets/general/images/no_poster.jpg";
	export let name = "Scarlett Johansson";
	export let popularity = "75.16";
	export let id = "123"



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


<a sveltekit:prefetch href={`/people/${slugify(name)}_${id}`}>
	<img src="https://image.tmdb.org/t/p/w500{image}" alt="{name}">

	<p class="name">{name}</p>
	<p class="rating">{popularity}</p>
</a>


<style>
	a{
		display: flex;
		align-items: center;
		justify-content: center;
		flex-direction: column;
	}
	img{
		border-radius: 2px;
		width: 13rem;
		height: 100%;
		object-fit: cover;
	}
	.name{
		background-color: #C84B31;
		padding: 0.2rem 0.2rem;
		font-size: 1.3rem;
		margin-top: 0.8rem;
	}
	.rating{
		font-size: 1.1rem;
		margin-top: 0.5rem;
	}
</style>