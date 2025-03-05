<script>
	import { json } from '@sveltejs/kit';
	import { onMount } from 'svelte';
	import { goto } from '$app/navigation';

	let itemDetails = 'Carregando...';
	let cartList = [];

	onMount(() => {
		itemDetails = localStorage.getItem('foodDetails');
		itemDetails = JSON.parse(itemDetails);
	});
	function addCart() {
		const savedCart = localStorage.getItem('cartList');
		if (savedCart) {
			cartList = JSON.parse(savedCart);
		}
		cartList.push(itemDetails);
		localStorage.setItem('cartList', JSON.stringify(cartList));
	}
	function goCheckout() {
		goto('/checkout');
	}
</script>

<div class="h-screen">
	<h1>{itemDetails.name}</h1>
	<h3>{itemDetails.details}</h3>
	<button class="cursor-pointer" on:click={addCart}>+</button>
	<button class="cursor-pointer" on:click={goCheckout}>checkout</button>
</div>
