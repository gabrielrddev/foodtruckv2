<script>
	import { onMount } from 'svelte';
	import ValidationQr from './ValidationQR.svelte';
	import { qrCodeValidated } from './store.js';

	let infoCheck = false;

	let cartCheckout = [];
	$: isValidated = $qrCodeValidated;
	onMount(() => {
		const savedCart = localStorage.getItem('cartList');
		cartCheckout = savedCart ? JSON.parse(savedCart) : [];
	});

	function removeList() {
		localStorage.removeItem('cartList');
		location.reload();
	}

	function removeItem(index) {
		cartCheckout.splice(index, 1);
		localStorage.setItem('cartList', JSON.stringify(cartCheckout));
		cartCheckout = cartCheckout;
	}

	function addMore(index) {
		if (cartCheckout[index]) {
			cartCheckout = [...cartCheckout, cartCheckout[index]];
			localStorage.setItem('cartList', JSON.stringify(cartCheckout));
		}
	}
	$: totalCarrinho = cartCheckout.reduce((total, item) => total + item.price, 0);
</script>

<div>
	{#if cartCheckout.length > 0}
		<div>
			{#each cartCheckout as item, index (index)}
				<div>
					<span>{item.name}</span>
					<span>R$ {item.price.toFixed(2)}</span>
					<button on:click={() => removeItem(index)}>Remover</button>
					<button on:click={() => addMore(index)}>Adicionar</button>
				</div>
			{/each}
			<div>
				Total: R$ {totalCarrinho.toFixed(2)}
			</div>
		</div>
	{:else}
		<p>Nenhum item no carrinho</p>
	{/if}
	{#if isValidated == true}
		<div>
			<button on:click={removeList} disabled={cartCheckout.length === 0}>Realizar Pedido</button>
		</div>
	{:else}
		<div>
			<button
				class="text-gray-600"
				on:click={() => (infoCheck = true)}
				disabled={cartCheckout.length === 0}>Realizar Pedido</button
			>
			{#if infoCheck}
				<p>porfavor primeiro escanei o qrcode com algum garçom para finalizar seu pedido</p>
			{/if}
		</div>
	{/if}
</div>
