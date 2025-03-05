<script>
	import { goto } from '$app/navigation';
	import Foods from '$lib/mock/Foods.json';

	let option = '';

	function getFoods(option) {
		switch (option) {
			case 'entradas':
				return Foods.entradas;
			case 'lanches':
				return Foods.lanches;
			case 'bebidas':
				return Foods.bebidas;
			case 'sobremesas':
				return Foods.sobremesas;
			default:
				return [];
		}
	}
	function infoDetails(item) {
		localStorage.setItem('foodDetails', JSON.stringify(item));
		goto('/foodDetails');
	}
	function goCheckout() {
		goto('/checkout');
	}
</script>

<div class="h-screen">
	<div>
		<ul>
			<li>
				<button class=" cursor-pointer" on:click={() => (option = 'entradas')}>Entradas</button>
			</li>
			<li>
				<button class=" cursor-pointer" on:click={() => (option = 'lanches')}>Lanches</button>
			</li>
			<li>
				<button class=" cursor-pointer" on:click={() => (option = 'bebidas')}>Bebidas</button>
			</li>
			<li>
				<button class=" cursor-pointer" on:click={() => (option = 'sobremesas')}>Sobremesas</button>
			</li>
		</ul>
	</div>
	<div class="flex flex-col">
		{#each getFoods(option) as item}
			<button on:click={() => infoDetails(item)} class="cursor-pointer"
				>{item.name} {item.price + ' R$'}</button
			>
		{/each}
		<button class="cursor-pointer" on:click={goCheckout}>checkout</button>
	</div>
</div>
