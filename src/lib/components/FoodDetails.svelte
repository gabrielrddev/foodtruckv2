<script>
	import { json } from '@sveltejs/kit';
	import { onMount } from 'svelte';
	import { goto } from '$app/navigation';
	import arrowback_icon from '$lib/assets/arrowback_icon.svg';
	import addcart_icon from '$lib/assets/addcart_icon.svg';
	import addwhite_icon from '$lib/assets/addwhite_icon.svg';

	let itemDetails = 'Carregando...';
	let cartList = [];
	let selected = false;
	let obsInput = '';

	onMount(() => {
		itemDetails = localStorage.getItem('foodDetails');
		itemDetails = JSON.parse(itemDetails);
	});

	function addCart() {
		const savedCart = localStorage.getItem('cartList');
		if (savedCart) {
			cartList = JSON.parse(savedCart);
		}

		const itemWithObs = { ...itemDetails, observation: obsInput };

		cartList.push(itemWithObs);
		localStorage.setItem('cartList', JSON.stringify(cartList));

		selected = true;
		setTimeout(() => {
			selected = false;
		}, 400);
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
		<img class="flex size-4 justify-center opacity-70" src={arrowback_icon} alt="" />
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
		<div class="rounded-lg bg-yellow-50 p-3 text-sm text-yellow-700">
			<input
				bind:value={obsInput}
				class="w-full"
				type="text"
				placeholder="Adicionar alguma observação."
			/>
		</div>
		<div class="mt-6 flex space-x-3">
			<button
				on:click={addCart}
				class="flex flex-1 items-center justify-center rounded-lg py-3 font-bold text-white shadow-md transition-all hover:bg-red-600 focus:ring-2 focus:outline-none"
				class:bg-green-500={selected}
				class:bg-red-500={!selected}
				class:focus:ring-red-400={!selected}
				class:focus:ring-green-400={selected}
			>
				<img src={addwhite_icon} alt="" />
				Adicionar ao Carrinho
			</button>
			<button
				on:click={goCheckout}
				class="flex flex-1 items-center justify-center rounded-lg bg-gray-700 py-3 font-bold text-white shadow-md transition-all hover:bg-gray-800 focus:ring-2 focus:ring-gray-500 focus:outline-none"
			>
				<img src={addcart_icon} alt="" />
				Checkout
			</button>
		</div>
	</div>
</div>
