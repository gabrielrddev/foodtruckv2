<script>
	import { goto } from '$app/navigation';
	import { onMount } from 'svelte';
	function handleErrorPage() {
		goto('/');
	}
	onMount(() => {
		handleErrorPage();
	});
</script>
