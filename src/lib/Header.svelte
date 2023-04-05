<script lang="ts">
	import { onMount } from "svelte";
	import { tweened } from "svelte/motion";
	import { quadOut } from 'svelte/easing';

	const HEADER_HEIGHT = 120;

	let y_pos = tweened(0, { easing: quadOut });
	let last_top = 0;

	onMount(() => {
		// borrowed from
		// https://stackoverflow.com/questions/31223341/detecting-scroll-direction
		document.addEventListener("scroll", (ev) => {
			let st = window.pageYOffset || document.documentElement.scrollTop;

			if (st > last_top && st > HEADER_HEIGHT) {
				// scrolling down and passed the point of invisible
				y_pos.set(HEADER_HEIGHT);
			} else if (st < last_top) {
				// scrolled up
				y_pos.set(0);
			}

			last_top = st <= 0 ? 0 : st;
		});
	});

</script>

<header style={`transform: translateY(-${$y_pos}px)`}>
	<div class="main">
		<h1>m</h1>
	</div>
	<div class="sec">
		<h2>b</h2>
	</div>
</header>

<style lang="scss">
	@use "$lib/sass/header" as h;

	header {
		position: fixed;
		top: 0;
		left: 0;

		height: h.$height;
		width: 100vw;

		display: grid;
		grid-template-columns: 100vw;
		grid-template-rows: h.$main_height h.$sub_height;

		.main, .sec {
			width: 100%;
			height: 100%;
		}

		.main {
			background-color: purple;
		}

		.sec {
			background-color: green;
		}
	}
</style>