<script>
	import { onMount } from 'svelte';
    import { goto } from '$app/navigation';
    import { dashboardValidate } from './store';

	onMount(async () => {
  if ($dashboardValidate === false) {
   goto('/admin/login');
  } 
 });
</script>

{#if $dashboardValidate}
	<slot />
{/if}
