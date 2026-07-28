<script>
	// Stylesheet (used automatically)
	import '../app.css';

	// Props
	let { children } = $props();
</script>

<!-- Main page contents -->
<main class="main-contents">
	{@render children()}
</main>


<style>
	.main-contents {
		padding: calc(var(--spacing) * 8);
	}
</style>