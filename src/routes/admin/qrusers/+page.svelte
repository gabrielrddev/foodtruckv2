<script>
	import NavBar from '$lib/components/NavBar.svelte';
	import DashBoardGuard from '$lib/components/DashBoardGuard.svelte';
	import { onMount } from 'svelte';

	let info = { message: [] };
	let showModal = false;
	let newUserName = '';

	onMount(() => {
		fetch('https://backendfoodtruck-production.up.railway.app/qruserslist')
			.then((response) => response.text())
			.then((result) => {
				info = JSON.parse(result);
			})
			.catch((error) => console.error(error));
	});

	function openModal() {
		showModal = true;
	}

	function closeModal() {
		showModal = false;
		newUserName = '';
	}

	async function addUser() {
		if (newUserName.trim() === '') {
			alert('Digite um nome válido.');
			return;
		}

		const myHeaders = new Headers();
		myHeaders.append('Content-Type', 'application/json');

		const raw = JSON.stringify({ name: newUserName });

		try {
			const response = await fetch('http://localhost:3000/addqruser', {
				method: 'POST',
				headers: myHeaders,
				body: raw,
				redirect: 'follow'
			});
			const result = await response.json();

			info.message = [...info.message, result.data];
			closeModal();
		} catch (error) {
			console.error(error);
		}
	}

	function deleteUser(index) {
		let deleteItemList = info.message[index];

		const myHeaders = new Headers();
		myHeaders.append('Content-Type', 'application/json');

		const raw = JSON.stringify({ id: deleteItemList.id });

		fetch('https://backendfoodtruck-production.up.railway.app/deleteqruser', {
			method: 'POST',
			headers: myHeaders,
			body: raw,
			redirect: 'follow'
		})
			.then((response) => response.text())
			.then(() => {
				info.message.splice(index, 1);
				info.message = [...info.message];
			})
			.catch((error) => console.error(error));
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
							<th class="text-xl cursor-pointer" on:click={openModal}>+</th>
						</tr>
					</thead>
					<tbody>
						{#each info.message as x, index}
							<tr class="border-b border-gray-100 hover:bg-gray-50">
								<td class="px-6 py-3">{x.id}</td>
								<td class="px-6 py-3">{x.name}</td>
								<td class="px-6 py-3">
									<button
										on:click={() => deleteUser(index)}
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

	{#if showModal}
		<div class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
			<div class="bg-white p-6 rounded-lg shadow-lg w-80">
				<h2 class="text-xl font-semibold mb-4 text-center">Adicionar Garçom</h2>
				<input
					type="text"
					placeholder="Nome do garçom"
					bind:value={newUserName}
					class="w-full px-4 py-2 border rounded-lg mb-4"
				/>
				<div class="flex justify-end space-x-4">
					<button
						on:click={closeModal}
						class="px-4 py-2 bg-gray-300 rounded-lg hover:bg-gray-400"
					>
						Cancelar
					</button>
					<button
						on:click={addUser}
						class="px-4 py-2 bg-green-600 text-white rounded-lg hover:bg-green-700"
					>
						Adicionar
					</button>
				</div>
			</div>
		</div>
	{/if}
</div>
