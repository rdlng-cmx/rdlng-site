<script lang="ts">
	import './layout.css';
	import '../lib/global.css';
	import favicon from '$lib/assets/favicon.svg';
	import Menu from '$lib/Menu';
	import { page } from '$app/state';

	
	let { children } = $props();
</script>
<svelte:head><link rel="icon" href={favicon} /></svelte:head>
<main class="w-screen h-dvh flex items-center justify-center">
<div id="main-but-real" class="flex flex-none h-full w-full max-w-180 py-2.5 gap-2.5">
<div class="flex-none overflow-x-visible overflow-y-clip">
<Menu/>
</div>
	{@render children()}

</div>
</main>