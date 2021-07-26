<script>
	import Navbar from '$lib/Navbar.svelte';
	import Footer from '$lib/Footer.svelte';
</script>




	<Navbar />
	<slot />
	<Footer />



<style>
	:global(*){
		margin: 0;
		padding: 0;
		box-sizing: border-box;
		font-family: 'Poppins', sans-serif !important;
		font-size: 100%;
		font-weight: 400;
		list-style: none;
		background-color: #161616;
		color: #DDDDDD;
		line-height: 1.5;
	}
	:global(.page-title){
		font-size: 2rem;
		background-color: #C84B31;
		padding: 0rem 0.7rem;
	}
	:global(a){
		text-decoration: none;
		color: inherit;
	}
	:global(img){
		display: inline-block;
		width: 100%;
		vertical-align: middle;
	}
	:global(::-webkit-scrollbar) {
	    width: 10px;
	}

	:global(::-webkit-scrollbar-track) {

	    background-color: inherit;
	}
	:global(::-webkit-scrollbar-thumb) {
		border-radius: 5px;
		background-color: #C84B31;
	    box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.3);
	}
</style>
