<script lang="ts">
	import Header from "$lib/Header.svelte";
	import Footer from "$lib/Footer.svelte";
</script>

<Header />

<main>
	<slot />
</main>

<Footer/>

<style lang="scss">
	@use "$lib/sass/header" as header;

	:global(body) {
		margin-top: header.$height;
	}
</style>