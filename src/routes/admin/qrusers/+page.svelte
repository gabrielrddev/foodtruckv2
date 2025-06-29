<script>
	import NavBar from '$lib/components/NavBar.svelte';
	import DashBoardGuard from '$lib/components/DashBoardGuard.svelte';
	import { onMount } from 'svelte';

	let info = $state([]);
	//tenho que ter um gerador de qrcode e ele pode pegar apartir do input do nome do cliente mais o horario
	//tenho que adicionar a parte para conseguir adicionar o usuario


	onMount(() => {
		const requestOptions = {
			method: 'GET',
			redirect: 'follow'
		};

		fetch('https://backendfoodtruck-production.up.railway.app/qruserslist', requestOptions)
			.then((response) => response.text())
			.then((result) => {
				info = JSON.parse(result);
				console.log(info.message);
			})
			.catch((error) => console.error(error));
	});

	function deleteUser(index) {
		let deleteItemList = info.message[index];
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

		fetch('https://backendfoodtruck-production.up.railway.app/deleteqruser', requestOptions)
			.then((response) => response.text())
			.then((result) => {
				console.log(result);
			})
			.catch((error) => console.error(error));

		info.message.splice(index, 1);
		info.message = [...info.message];
	}
</script>

<div>
	<DashBoardGuard>
		<NavBar />
		<div class="bg-white min-h-screen px-6 py-8">
			<h1 class="text-2xl font-semibold text-gray-800 mb-6 text-center">Garçom</h1>
			<div class="overflow-x-auto">
				<table class="w-full text-sm text-left text-gray-700 bg-white shadow rounded-lg">
					<thead class="uppercase text-xs text-gray-600 bg-gray-100 border-b border-gray-200">
						<tr>
							<th class="px-6 py-4">ID</th>
							<th class="px-6 py-4">Nome</th>
							<th class="px-6 py-4">Ações</th>
							<th class=" text-xl cursor-pointer">+</th>
						</tr>
					</thead>
					<tbody>
						{#each info.message as x, index}
							<tr class="border-b border-gray-100 hover:bg-gray-50">
								<td class="px-6 py-3">{x.id}</td>
								<td class="px-6 py-3">{x.name}</td>
								<td class="px-6 py-3">
									<button
										onclick={() => deleteUser(index)}
										class="px-4 py-2 bg-red-600 text-white rounded-lg hover:bg-red-700 transition duration-200"
									>
										Excluir
									</button>
								</td>
							</tr>
						{/each}
					</tbody>
				</table>
			</div>
		</div>
	</DashBoardGuard>
</div>
