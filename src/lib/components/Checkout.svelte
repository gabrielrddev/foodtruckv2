<script>
	import { onMount } from 'svelte';
	import ValidationQr from './ValidationQR.svelte';
	import { qrCodeValidated } from './store.js';
	import { goto } from '$app/navigation';
	import delete_icon from '$lib/assets/delete_icon.svg';
	import add_icon from '$lib/assets/add_icon.svg';
	import arrowback_icon from '$lib/assets/arrowback_icon.svg';
	import cart_icon from '$lib/assets/cart_icon.svg';

	let numberTable;
	let comfirmed = false;
	let infoCheck = false;
	let cartCheckout = [];
	let sendBack = [];

	//send backend data
	let foodName;
	let foodObservation;
	let foodPrice;
	let foodDetails;
	let foodTable;

	$: isValidated = $qrCodeValidated;
	let isValidated2 = false;

	$: if (numberTable >= 1 || numberTable <= 10) {
		isValidated2 = true;
	}

	onMount(() => {
		const savedCart = localStorage.getItem('cartList');
		cartCheckout = savedCart ? JSON.parse(savedCart) : [];
	});

	function sendList() {
		const cartItems = localStorage.getItem('cartList');
		if (cartItems) {
			sendBack = JSON.parse(cartItems);
		}

		const itemsWithTable = sendBack.map((item) => ({
			...item,
			numeromesa: numberTable
		}));

		localStorage.setItem('sendBack', JSON.stringify(itemsWithTable));

		itemsWithTable.forEach((item, index) => {
			console.log(item);
			//console.log(`Item ${index + 1}:`);
			//console.log(`  ID: ${item.id}`); esses dois podem ser configurados a mais
			foodName = item.name;
			foodPrice = item.price;
			foodDetails = item.details;
			foodObservation = item.observation;
			foodTable = item.numeromesa;

			const myHeaders = new Headers();
			myHeaders.append('Content-Type', 'application/json');
			const raw = JSON.stringify({
				FoodName: foodName,
				FoodPrice: foodPrice,
				FoodDetails: foodDetails,
				FoodObservation: foodObservation,
				FoodTable: foodTable
			});

			const requestOptions = {
				method: 'POST',
				headers: myHeaders,
				body: raw,
				redirect: 'follow'
			};

			fetch('https://backendfoodtruck-production.up.railway.app/receivecart', requestOptions)
				.then((response) => response.text())
				.then((result) => console.log(result))
				.catch((error) => console.error(error));
		});

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
		cartCheckout = [...cartCheckout];
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
			<img class="flex size-4 justify-center opacity-70" src={arrowback_icon} alt="" />
			Voltar
		</button>
		<h1 class="text-xl font-bold text-gray-800">Seu Carrinho</h1>
	</div>
	{#if comfirmed}
		<div class="-mt-3 mb-2 rounded-lg bg-green-50 p-3 text-sm text-green-700">
			<p>Pedido realizado com sucesso!</p>
		</div>
	{/if}

	{#if cartCheckout.length > 0}
		<div class="mb-32 rounded-xl bg-white p-4 shadow-md">
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
									class="flex h-9 w-9 items-center justify-center rounded-lg bg-gray-200 text-gray-700 hover:bg-red-100 hover:text-red-500 focus:ring-2 focus:ring-red-400 focus:outline-none"
								>
									<img class="size-6 opacity-70" src={delete_icon} alt="" />
								</button>
								<button
									on:click={() => addMore(index)}
									class="flex h-9 w-9 items-center justify-center rounded-lg bg-gray-200 p-2 text-gray-700 hover:bg-green-100 hover:text-green-500 focus:ring-2 focus:ring-green-400 focus:outline-none"
								>
									<img class="size-10 opacity-70" src={add_icon} alt="" />
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
			<img class="size-20 opacity-60" src={cart_icon} alt="" />
			<p class="text-lg font-medium text-gray-600">Nenhum item no carrinho</p>
			<button
				on:click={goBack}
				class="mt-4 rounded-lg bg-red-500 px-4 py-2 font-medium text-white shadow-md hover:bg-red-600 focus:ring-2 focus:ring-red-400 focus:outline-none"
			>
				Continuar Comprando
			</button>
		</div>
	{/if}
	<div class="fixed right-0 bottom-0 left-0 bg-white p-4 shadow-lg">
		{#if isValidated}<input
				class="mb-2 w-full rounded-lg bg-yellow-50 p-3 text-sm text-yellow-700"
				type="number"
				bind:value={numberTable}
				placeholder="Porfavor insira o numero da mesa!"
			/>
			{#if isValidated2}
				<button
					on:click={sendList}
					disabled={cartCheckout.length === 0}
					class="w-full rounded-lg bg-red-500 py-3 font-bold text-white shadow-md hover:bg-red-600 focus:ring-2 focus:ring-red-400 focus:outline-none disabled:opacity-50"
				>
					Realizar Pedido
				</button>
			{:else}
				<button
					class="w-full rounded-lg bg-gray-300 py-3 font-bold text-gray-600 shadow-md hover:bg-gray-400 focus:ring-2 focus:ring-gray-400 focus:outline-none disabled:opacity-50"
					on:click={() => (infoCheck = true)}
					disabled={cartCheckout.length === 0}
				>
					Realizar Pedido
				</button>
			{/if}
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
