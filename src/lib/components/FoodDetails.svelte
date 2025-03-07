<script>
	import { json } from '@sveltejs/kit';
	import { onMount } from 'svelte';
	import { goto } from '$app/navigation';
	let itemDetails = 'Carregando...';
	let cartList = [];
	onMount(() => {
		itemDetails = localStorage.getItem('foodDetails');
		itemDetails = JSON.parse(itemDetails);
		console.log(itemDetails);
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
	function goBack() {
		goto('/');
	}
</script>

<div class="min-h-screen bg-gray-50 p-4">
	<button
		on:click={goBack}
		class="mb-4 flex items-center rounded-lg bg-white px-3 py-2 text-gray-700 shadow-md transition-all hover:bg-gray-100 focus:ring-2 focus:ring-red-400 focus:outline-none"
	>
		<svg
			xmlns="http://www.w3.org/2000/svg"
			class="mr-1 h-5 w-5"
			fill="none"
			viewBox="0 0 24 24"
			stroke="currentColor"
		>
			<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 19l-7-7 7-7" />
		</svg>
		Voltar
	</button>
	<div class="overflow-hidden rounded-xl bg-white p-4 shadow-md">
		<div class="mb-4 flex justify-center">
			<img
				class="h-48 w-full rounded-lg object-cover sm:w-64"
				src={`/images/${itemDetails.id}.jpg`}
				alt={itemDetails.id}
			/>
		</div>
		<div class="mb-6">
			<div class="flex items-center justify-between">
				<h1 class="text-2xl font-bold text-gray-800">{itemDetails.name}</h1>
				<span class="rounded-sm bg-red-500 px-3 py-1 text-sm font-bold text-white">
					{itemDetails.price} R$
				</span>
			</div>
			<h3 class="mt-4 text-gray-600">{itemDetails.details}</h3>
		</div>

		<div class="mt-6 flex space-x-3">
			<button
				on:click={addCart}
				class="flex flex-1 items-center justify-center rounded-lg bg-red-500 py-3 font-bold text-white shadow-md transition-all hover:bg-red-600 focus:ring-2 focus:ring-red-400 focus:outline-none"
			>
				<svg
					xmlns="http://www.w3.org/2000/svg"
					class="mr-2 h-5 w-5"
					fill="none"
					viewBox="0 0 24 24"
					stroke="currentColor"
				>
					<path
						stroke-linecap="round"
						stroke-linejoin="round"
						stroke-width="2"
						d="M12 4v16m8-8H4"
					/>
				</svg>
				Adicionar ao Carrinho
			</button>
			<button
				on:click={goCheckout}
				class="flex flex-1 items-center justify-center rounded-lg bg-gray-700 py-3 font-bold text-white shadow-md transition-all hover:bg-gray-800 focus:ring-2 focus:ring-gray-500 focus:outline-none"
			>
				<svg
					xmlns="http://www.w3.org/2000/svg"
					class="mr-2 h-5 w-5"
					fill="none"
					viewBox="0 0 24 24"
					stroke="currentColor"
				>
					<path
						stroke-linecap="round"
						stroke-linejoin="round"
						stroke-width="2"
						d="M16 11V7a4 4 0 00-8 0v4M5 9h14l1 12H4L5 9z"
					/>
				</svg>
				Checkout
			</button>
		</div>
	</div>
</div>
