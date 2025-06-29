<script>
	import { onMount } from 'svelte';
	import NavBar from '$lib/components/NavBar.svelte';
	import DashBoardGuard from '$lib/components/DashBoardGuard.svelte';
	let info = {};
	let info2 = [];
	let interval;

	function fetchList() {
		const requestOptions = {
			method: 'GET',
			redirect: 'follow'
		};

		fetch('https://backendfoodtruck-production.up.railway.app/infolist', requestOptions)
			.then((response) => response.text())
			.then((result) => {
				info = JSON.parse(result);
				console.log(info.message);
				info2 = info.message;
			})
			.catch((error) => console.error(error));
	}

	onMount(() => {
		fetchList();
		setInterval(fetchList, 5000);
		console.log(info2)
	});

	function deleteFood(index) {
		let deleteItemList = info2[index];
		console.log(deleteItemList.id);

		const myHeaders = new Headers();
		myHeaders.append('Content-Type', 'application/json');

		const raw = JSON.stringify({
			id: deleteItemList.id
		});

		const requestOptions = {
			method: 'POST',
			headers: myHeaders,
			body: raw,
			redirect: 'follow'
		};

		fetch('https://backendfoodtruck-production.up.railway.app/deletelist', requestOptions)
			.then((response) => response.text())
			.then((result) => {
				console.log(result);
			})
			.catch((error) => console.error(error));
		info2.splice(index, 1);
		info2 = [...info2];
	}

	function finishedFood(index) {
		let finishedList = info2[index];
		console.log(finishedList);

		const myHeaders = new Headers();
		myHeaders.append("Content-Type", "application/json");

		const raw = JSON.stringify({
			"id": finishedList.id,
			"FoodPrice": finishedList.price,
			"FoodName": finishedList.name,
			"FoodDetails": finishedList.details,
			"FoodObservation": finishedList.observation,
			"FoodTable": finishedList.numbertable
		});

		const requestOptions = {
			method: "POST",
			headers: myHeaders,
			body: raw,
			redirect: "follow"
		};

		fetch("https://backendfoodtruck-production.up.railway.app/finishedlist", requestOptions)
			.then((response) => response.text())
			.then((result) => console.log(result))
			.catch((error) => console.error(error));

		deleteFood(index);
	}
</script>

<div class="bg-white min-h-screen px-4 py-6">
	<DashBoardGuard> 
		<NavBar />
		<h1 class="text-xl font-semibold text-gray-800 mb-6 text-center">Pedidos Atuais</h1>

		<div class="overflow-x-auto">
		{#if info2 == 'nao ah pedidos na fila' || info2 == [] || info2 == '' || info2 == null || info2 == undefined || info.message == 'nao ah pedidos na fila'}
			<h1>nao a pedidos</h1>
			{:else}
			<table class="w-full text-sm text-left text-gray-700 border border-gray-200 rounded-lg shadow-sm">
				<thead class="text-xs text-gray-600 uppercase bg-gray-100 border-b border-gray-200">
					<tr>
						<th class="px-4 py-3">ID</th>
						<th class="px-4 py-3">Nome</th>
						<th class="px-4 py-3">Preço</th>
						<th class="px-4 py-3">Observação</th>
						<th class="px-4 py-3">Mesa</th>
						<th class="px-4 py-3">Ações</th>
					</tr>
				</thead>
				<tbody>
					{#each info2 as x, index}
						<tr class="border-b border-gray-100 hover:bg-gray-50">
							<td class="px-4 py-3">{x.id}</td>
							<td class="px-4 py-3">{x.name}</td>
							<td class="px-4 py-3">R$ {x.price}</td>
							<td class="px-4 py-3">{x.observation}</td>
							<td class="px-4 py-3">{x.numbertable}</td>
							<td class="px-4 py-3 flex space-x-2">
								<button
									onclick={() => deleteFood(index)}
									class="px-3 py-1 text-sm text-red-600 border border-red-500 rounded hover:bg-red-50 transition"
								>
									Cancelar
								</button>
								<button
									onclick={() => finishedFood(index)}
									class="px-3 py-1 text-sm text-white bg-red-500 rounded hover:bg-red-600 transition"
								>
									Finalizar
								</button>
							</td>
						</tr>
					{/each}
				</tbody>
			</table>
			{/if}
		</div>
	</DashBoardGuard>
</div>
