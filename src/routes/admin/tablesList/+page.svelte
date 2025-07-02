<script>
	import { onMount } from 'svelte';
	import NavBar from '$lib/components/NavBar.svelte';
	import DashBoardGuard from '$lib/components/DashBoardGuard.svelte';

	let info = {};
	let info2 = [];
	let groupedTables = {};

	function fetchList() {
		const requestOptions = {
			method: 'GET',
			redirect: 'follow'
		};

		fetch('https://backendfoodtruck-production.up.railway.app/tablelist', requestOptions)
			.then((response) => response.text())
			.then((result) => {
				info = JSON.parse(result);
				info2 = info.message;
				groupTables();
			})
			.catch((error) => console.error(error));
	}

	function groupTables() {
		groupedTables = {};
		for (const item of info2) {
			const table = item.numbertable;
			if (!groupedTables[table]) {
				groupedTables[table] = [];
			}
			groupedTables[table].push(item);
		}
	}

	onMount(() => {
		fetchList();
		setInterval(fetchList, 5000);
	});

	function deleteFood(index, table) {
		let deleteItemList = groupedTables[table][index];

		const myHeaders = new Headers();
		myHeaders.append('Content-Type', 'application/json');

		const raw = JSON.stringify({ id: deleteItemList.id });

		const requestOptions = {
			method: 'POST',
			headers: myHeaders,
			body: raw,
			redirect: 'follow'
		};

		fetch('https://backendfoodtruck-production.up.railway.app/deletelisttable', requestOptions)
			.then((response) => response.text())
			.then((result) => console.log(result))
			.catch((error) => console.error(error));

		groupedTables[table].splice(index, 1);
		groupedTables = { ...groupedTables };
	}

	// NOVA FUNÇÃO sem alterar o backend
	function clearTable(table) {
		const itemsCopy = [...groupedTables[table]];
		itemsCopy.forEach((_, index) => {
			// sempre remove o primeiro item porque a lista vai se atualizando
			deleteFood(0, table);
		});
	}

	// Função para calcular o total da mesa
	function calculateTotal(table) {
		return groupedTables[table].reduce((total, item) => total + parseFloat(item.price), 0).toFixed(2);
	}
</script>

<DashBoardGuard>
	<NavBar />
	<div class="bg-white min-h-screen px-4 py-6">
		<h1 class="text-xl font-semibold text-gray-800 mb-6 text-center">Pedidos por Mesa</h1>

		<div class="space-y-10">
			{#each Object.entries(groupedTables) as [table, items]}
				<div class="border border-gray-200 rounded-lg shadow-sm">
					<div class="flex items-center justify-between bg-gray-100 px-4 py-3 rounded-t-md">
						<h2 class="text-sm font-semibold text-gray-700">Mesa {table}</h2>
						<button
							on:click={() => clearTable(table)}
							class="text-xs bg-red-500 hover:bg-red-600 text-white px-3 py-1 rounded transition"
						>
							Finalizar Mesa
						</button>
					</div>
					<div class="overflow-x-auto">
						<table class="w-full text-sm text-left text-gray-700">
							<thead class="uppercase text-xs text-gray-600 bg-gray-50 border-b border-gray-200">
								<tr>
									<th class="px-4 py-3">ID</th>
									<th class="px-4 py-3">Nome</th>
									<th class="px-4 py-3">Preço</th>
									<th class="px-4 py-3">Observação</th>
									<th class="px-4 py-3">Ações</th>
								</tr>
							</thead>
							<tbody>
								{#each items as item, index}
									<tr class="border-b border-gray-100 hover:bg-gray-50">
										<td class="px-4 py-2">{item.id}</td>
										<td class="px-4 py-2">{item.name}</td>
										<td class="px-4 py-2">R$ {item.price}</td>
										<td class="px-4 py-2">{item.observation}</td>
										<td class="px-4 py-2">
											<button
												on:click={() => deleteFood(index, table)}
												class="px-3 py-1 text-sm text-white bg-red-500 rounded hover:bg-red-600 transition"
											>
												Item Pago
											</button>
										</td>
									</tr>
								{/each}
							</tbody>
						</table>
					</div>

					<!-- Exibe o total da mesa -->
					<div class="px-4 py-2 text-right text-sm font-semibold text-gray-700">
						Total: R$ {calculateTotal(table)}
					</div>
				</div>
			{/each}
		</div>
	</div>
</DashBoardGuard>
