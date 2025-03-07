<script>
	import { onMount } from 'svelte';
	import ValidationQr from './ValidationQR.svelte';
	import { qrCodeValidated } from './store.js';
	import { goto } from '$app/navigation';

	let comfirmed = false;
	let infoCheck = false;
	let cartCheckout = [];
	$: isValidated = $qrCodeValidated;

	onMount(() => {
		const savedCart = localStorage.getItem('cartList');
		cartCheckout = savedCart ? JSON.parse(savedCart) : [];
	});

	function removeList() {
		localStorage.removeItem('cartList');
		comfirmed = true;
		setTimeout(() => {
			comfirmed = false;
			location.reload();
		}, 2000);
	}

	function removeItem(index) {
		cartCheckout.splice(index, 1);
		localStorage.setItem('cartList', JSON.stringify(cartCheckout));
		cartCheckout = [...cartCheckout]; // Corrigido para forçar atualização reativa
	}

	function addMore(index) {
		if (cartCheckout[index]) {
			cartCheckout = [...cartCheckout, cartCheckout[index]];
			localStorage.setItem('cartList', JSON.stringify(cartCheckout));
		}
	}

	function goBack() {
		goto('/');
	}

	$: totalCarrinho = cartCheckout.reduce((total, item) => total + parseFloat(item.price), 0);
	$: itemCount = cartCheckout.length;
</script>

<div class="min-h-screen bg-gray-50 p-4">
	<div class="mb-6 flex items-center justify-between">
		<button
			on:click={goBack}
			class="flex items-center rounded-lg bg-white px-3 py-2 text-gray-700 shadow-md hover:bg-gray-100 focus:ring-2 focus:ring-red-400 focus:outline-none"
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
		<h1 class="text-xl font-bold text-gray-800">Seu Carrinho</h1>
	</div>
	{#if cartCheckout.length > 0}
		<div class="mb-20 rounded-xl bg-white p-4 shadow-md">
			<div class="mb-6 divide-y divide-gray-100">
				{#each cartCheckout as item, index}
					<div class="flex items-center justify-between py-4">
						<div class="flex items-center space-x-3">
							{#if item && item.id}
								<img
									class="h-16 w-16 rounded-lg object-cover"
									src={`/images/${item.id}.jpg`}
									alt={item.name}
								/>
							{/if}
							<div>
								<h3 class="font-medium text-gray-800">{item.name}</h3>
								{#if item && item.details}
									<p class="text-sm text-gray-500">{item.details.substring(0, 20)}...</p>
								{/if}
							</div>
						</div>

						<div class="flex items-center space-x-2">
							<span class="font-medium text-gray-800">R$ {parseFloat(item.price).toFixed(2)}</span>
							<div class="flex space-x-1">
								<button
									on:click={() => removeItem(index)}
									class="rounded-lg bg-gray-200 p-2 text-gray-700 hover:bg-red-100 hover:text-red-500 focus:ring-2 focus:ring-red-400 focus:outline-none"
								>
									<svg
										xmlns="http://www.w3.org/2000/svg"
										class="h-5 w-5"
										fill="none"
										viewBox="0 0 24 24"
										stroke="currentColor"
									>
										<path
											stroke-linecap="round"
											stroke-linejoin="round"
											stroke-width="2"
											d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16"
										/>
									</svg>
								</button>
								<button
									on:click={() => addMore(index)}
									class="rounded-lg bg-gray-200 p-2 text-gray-700 hover:bg-green-100 hover:text-green-500 focus:ring-2 focus:ring-green-400 focus:outline-none"
								>
									<svg
										xmlns="http://www.w3.org/2000/svg"
										class="h-5 w-5"
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
								</button>
							</div>
						</div>
					</div>
				{/each}
			</div>

			<div class="rounded-lg bg-gray-50 p-4">
				<div class="mb-2 flex justify-between">
					<span class="text-gray-600">Itens</span>
					<span class="font-medium text-gray-800">{itemCount}</span>
				</div>
				<div class="mb-4 flex justify-between">
					<span class="text-gray-600">Subtotal</span>
					<span class="font-medium text-gray-800">R$ {totalCarrinho.toFixed(2)}</span>
				</div>
				<div class="border-t border-gray-200 pt-4">
					<div class="flex justify-between">
						<span class="text-lg font-bold text-gray-800">Total</span>
						<span class="text-lg font-bold text-red-500">R$ {totalCarrinho.toFixed(2)}</span>
					</div>
				</div>
			</div>
		</div>
	{:else}
		<div
			class="mb-20 flex flex-col items-center justify-center rounded-xl bg-white p-8 text-center shadow-md"
		>
			<svg
				xmlns="http://www.w3.org/2000/svg"
				class="mb-4 h-16 w-16 text-gray-300"
				fill="none"
				viewBox="0 0 24 24"
				stroke="currentColor"
			>
				<path
					stroke-linecap="round"
					stroke-linejoin="round"
					stroke-width="2"
					d="M3 3h2l.4 2M7 13h10l4-8H5.4M7 13L5.4 5M7 13l-2.293 2.293c-.63.63-.184 1.707.707 1.707H17m0 0a2 2 0 100 4 2 2 0 000-4zm-8 2a2 2 0 11-4 0 2 2 0 014 0z"
				/>
			</svg>
			<p class="text-lg font-medium text-gray-600">Nenhum item no carrinho</p>
			<button
				on:click={goBack}
				class="mt-4 rounded-lg bg-red-500 px-4 py-2 font-medium text-white shadow-md hover:bg-red-600 focus:ring-2 focus:ring-red-400 focus:outline-none"
			>
				Continuar Comprando
			</button>
		</div>
	{/if}
	{#if comfirmed}
		<div class="-mt-10 rounded-lg bg-yellow-50 p-3 text-sm text-yellow-700">
			<p>Pedido realizado com sucesso!</p>
		</div>
	{/if}
	<div class="fixed right-0 bottom-0 left-0 bg-white p-4 shadow-lg">
		{#if isValidated}
			<button
				on:click={removeList}
				disabled={cartCheckout.length === 0}
				class="w-full rounded-lg bg-red-500 py-3 font-bold text-white shadow-md hover:bg-red-600 focus:ring-2 focus:ring-red-400 focus:outline-none disabled:opacity-50"
			>
				Realizar Pedido
			</button>
		{:else}
			<div class="space-y-2">
				<ValidationQr />
				<button
					class="w-full rounded-lg bg-gray-300 py-3 font-bold text-gray-600 shadow-md hover:bg-gray-400 focus:ring-2 focus:ring-gray-400 focus:outline-none disabled:opacity-50"
					on:click={() => (infoCheck = true)}
					disabled={cartCheckout.length === 0}
				>
					Realizar Pedido
				</button>
				{#if infoCheck}
					<div class="rounded-lg bg-yellow-50 p-3 text-sm text-yellow-700">
						<p>Por favor, escaneie o QR code com algum garçom para finalizar seu pedido</p>
					</div>
				{/if}
			</div>
		{/if}
	</div>
</div>
