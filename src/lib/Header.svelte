<script lang="ts">
	import { onMount } from 'svelte';
	import { tweened, type Tweened } from 'svelte/motion';
	import { quartOut } from 'svelte/easing';

	import SearchBarButton from './SearchBarButton.svelte';
	import CartButton from './CartButton.svelte';
	import UserButton from './UserButton.svelte';
	import Depts from './icons/depts.svg.svelte';

	let MAIN_HEADER_HEIGHT = 72;
	let SUB_HEADER_HEIGHT = 56;

	$: HEADER_HEIGHT = MAIN_HEADER_HEIGHT + SUB_HEADER_HEIGHT;

	const DOWN_DURATION: number = 450;

	let y_pos: Tweened<number> = tweened(0, {
		duration: (from, to) =>
			from >= HEADER_HEIGHT
				? from > to
					? DOWN_DURATION
					: 0
				: DOWN_DURATION,
		easing: quartOut,
	});

	let last_top: number = 0;

	onMount(() => {
		// borrowed from
		// https://stackoverflow.com/questions/31223341/detecting-scroll-direction
		document.addEventListener('scroll', (ev) => {
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

<header
	style={`transform: translateY(-${$y_pos}px)`}
	bind:clientHeight={HEADER_HEIGHT}
>
	<div class="main" bind:clientHeight={MAIN_HEADER_HEIGHT}>
		<a class="left" href="/">
			<h1 class="title">
				<span class="b">PB</span>Tech
			</h1>
		</a>

		<span style="flex: 1 1 auto;" />

		<div class="right">
			<SearchBarButton />
			<CartButton />
			<UserButton />
		</div>
	</div>
	<div class="sec" bind:clientHeight={SUB_HEADER_HEIGHT}>
		<span class="depts">
			<div>
				<Depts />
			</div>
			<span>Departments</span>
		</span>
	</div>
</header>

<style lang="scss">
	@use 'sass:math';
	@use 'sass:color';
	@use '$lib/sass/header' as h;
	@use '$lib/sass/colours' as c;

	$icon_size: 48px;
	$drop_shadow: drop-shadow(2px 2px 2px c.$background);

	header {
		position: fixed;
		top: 0;
		left: 0;

		height: h.$height;
		width: 100vw;

		display: grid;
		grid-template-columns: 100vw;
		grid-template-rows: h.$main_height h.$sub_height;

		.main,
		.sec {
			width: 100%;
			height: 100%;

			display: flex;
			flex-direction: row;
		}

		.main {
			background-color: c.$header-background;

			justify-content: space-between;
			align-items: center;

			.left {
				text-decoration: none;

				padding: 0 h.$main_height;
				height: 100%;
				display: grid;
				place-items: center;

				&:hover .title {
					filter: $drop_shadow;
				}

				.title {
					font-size: 32px;
					line-height: 48px;
					font-weight: 300;
					color: c.$text;
					
					transition: filter 100ms ease;

					filter: none;

					.b {
						color: c.$primary;
						font-weight: 700;
					}
				}
			}

			.right {
				display: grid;
				grid-template-columns: repeat(3, h.$main_height);
				grid-template-rows: 1fr;

				height: 100%;

				padding: 0 h.$main_height;


				:global(div) {
					display: grid;
					overflow: visible;
					place-items: center;

					cursor: pointer;

					width: 100%;
					height: 100%;
				}

				:global(div svg) {
					height: math.div($icon_size, 1.5);


					filter: none;
					transition: filter 100ms ease;
				}

				:global(div:hover svg) {
					filter: $drop_shadow;
				}
			}
		}

		.sec {
			background-color: c.$tertiary;

			.depts {
				$depts_hover: color.scale(c.$tertiary, $lightness: -20%);

				display: flex;
				justify-content: flex-start;
				align-items: center;

				background-color: c.$tertiary;

				cursor: pointer;

				border-right: 2px solid $depts_hover;

				transition: background-color 100ms ease;

				padding: 0 h.$main_height;

				div {
					width: h.$sub_height;
					height: h.$sub_height;

					display: flex;
					flex-direction: row;
					justify-content: flex-start;
					align-items: center;
				}

				:global(svg) {
					height: math.div($icon_size, 1.5);
				}

				span {
					color: c.$text;
					font-size: 16px;
				}

				&:hover {
					background-color: $depts_hover;
				}
			}
		}
	}
</style>
