<script context="module">
	export function load({ error, status }) {
		return {
			props: {
				title: status,
			}
		};
	}
</script>

<script>
	export let title;
</script>
<main>

	<h1>{title}</h1>
	<p>Server Error</p>
	<section>
		<a href="/">back to home page</a>
		<a href="javascript:location.reload(true)">Refresh this page</a>
	</section>
</main>




<style>
	main{
		width: 100%;
		height: 80vh;
		text-align: center;
		display: flex;
		align-items: center;
		justify-content: flex-start;
		flex-direction: column;
		user-select: none;
	}
	h1{
		background-color: transparent;
		margin: 0;
		padding: 0;
		margin-top: -2rem;
		font-size: 20rem;
	}
	p{
		margin-top: -5rem;
	}
	a{
		margin-top: 2rem;
		text-transform: uppercase;
		padding:0.5rem 1rem;
		border: 1px solid #C84B31;
	}
	a:hover{
		background-color: #C84B31;
	}
	section{
		display: flex;
	}
	section a{
		margin-right: 1rem;
	}
</style>