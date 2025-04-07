<script>
	import { goto } from '$app/navigation';
	
	let data = $state([]);
	let option = '';
	function getFoods(option) {
		let raw = JSON.stringify({
		foodType: option
	})
		const requestOptions = {
  		method: "POST",
  		headers: { "Content-Type": "application/json" },
  		body: raw
	};
		fetch("http://localhost:3000/menu", requestOptions)
  		.then((response) => response.json())
  		.then((result) => data = result.message )
  		.catch((error) => console.error(error));

			return data
	}
	function infoDetails(item) {
		console.log(item)
		localStorage.setItem('foodDetails', JSON.stringify(item));
		goto('/foodDetails');
	}
	function goCheckout() {
		goto('/checkout');
	}
</script>

<div class="min-h-screen bg-gray-50 p-4">
	<div class="mt-4 mb-6">
		<ul class="grid grid-cols-2 gap-3 md:grid-cols-4">
			<li>
				<button
					class="w-full rounded-lg bg-white px-4 py-3 text-center font-medium shadow-md transition-all hover:bg-red-500 hover:text-white focus:ring-2 focus:ring-red-400 focus:outline-none {option ===
					'entradas'
						? 'bg-red-500 text-gray-700'
						: 'text-gray-700'}"
					onclick={() => getFoods('entradas')}
				>
					Entradas
				</button>
			</li>
			<li>
				<button
					class="w-full rounded-lg bg-white px-4 py-3 text-center font-medium shadow-md transition-all hover:bg-red-500 hover:text-white focus:ring-2 focus:ring-red-400 focus:outline-none {option ===
					'lanches'
						? 'bg-red-500 text-gray-700'
						: 'text-gray-700'}"
					onclick={() => getFoods('lanches')}
				>
					Lanches
				</button>
			</li>
			<li>
				<button
					class="w-full rounded-lg bg-white px-4 py-3 text-center font-medium shadow-md transition-all hover:bg-red-500 hover:text-white focus:ring-2 focus:ring-red-400 focus:outline-none {option ===
					'bebidas'
						? 'bg-red-500 text-gray-700'
						: 'text-gray-700'}"
					onclick={() => getFoods('bebidas')}
				>
					Bebidas
				</button>
			</li>
			<li>
				<button
					class="w-full rounded-lg bg-white px-4 py-3 text-center font-medium shadow-md transition-all hover:bg-red-500 hover:text-white focus:ring-2 focus:ring-red-400 focus:outline-none {option ===
					'sobremesas'
						? 'bg-red-500 text-gray-700'
						: 'text-gray-700'}"
					onclick={() => getFoods('sobremesas')}
				>
					Sobremesas
				</button>
			</li>
		</ul>
	</div>
	{#if data.length === 0}
		<div class=" rounded-lg bg-yellow-50 p-3 text-sm text-yellow-700">
			<h1>Escolha qual categoria se encaixa com oque voce deseja pedir!</h1>
		</div>
	{/if}
	<div class="mb-20 space-y-4">
		{#each data as item}
			<button
				onclick={() => infoDetails(item)}
				class="w-full overflow-hidden rounded-xl bg-white p-3 text-left shadow-md transition-all hover:shadow-lg focus:ring-2 focus:ring-red-400 focus:outline-none"
			>
				<div class="flex items-center justify-between">
					<div class="flex items-center space-x-3">
						<img
							class="h-20 w-20 rounded-lg object-cover"
							src={`/images/${item.id}.jpg`}
							alt={item.name}
						/>
						<div>
							<h3 class="font-bold text-gray-800">{item.name}</h3>
							<p class="mt-1 text-sm text-gray-600">{item.details.substring(0, 30)}...</p>
						</div>
					</div>
					<div class="flex flex-col items-end">
						<span
							class="-mt-10 flex w-12 justify-center rounded-sm bg-red-500 text-xs font-bold text-white"
						>
							{item.price} R$
						</span>
					</div>
				</div>
			</button>
		{/each}
	</div>

	<div class="fixed right-0 bottom-0 left-0 bg-white p-4 shadow-lg">
		<button
			onclick={goCheckout}
			class="w-full rounded-lg bg-red-500 py-3 font-bold text-white shadow-md transition-all hover:bg-red-600 focus:ring-2 focus:ring-red-400 focus:outline-none"
		>
			Ir para Checkout
		</button>
	</div>
</div>
