<script>
	import { goto } from '$app/navigation';
	import { dashboardValidate } from '$lib/components/store';
	let username = $state('');
	let password = $state('');
	let info = $state('');
	async function validation() {
		const myHeaders = new Headers();
		myHeaders.append('Content-Type', 'application/json');

		const raw = JSON.stringify({
			email: username,
			password: password
		});

		const requestOptions = {
			method: 'POST',
			headers: myHeaders,
			body: raw,
			redirect: 'follow'
		};

		await fetch('https://backendfoodtruck-production.up.railway.app/loginadmin', requestOptions)
			.then((response) => response.text())
			.then((result) => (info = JSON.parse(result)))
			.catch((error) => console.error(error));
		console.log(info.message);
		if (info.message === 'valido') {
			dashboardValidate.set(true)
			goto('/admin/foodList');
		}
	}
</script>

<div class="min-h-screen bg-white flex items-center justify-center px-4">
	<div class="w-full max-w-sm bg-white rounded-xl shadow-sm border border-gray-200 p-6 space-y-6">
		<h1 class="text-xl font-semibold text-center text-gray-800">Login</h1>
		
		<form action="submit" class="space-y-4">
			<input 
				type="text" 
				bind:value={username} 
				placeholder="Usuário" 
				class="w-full px-4 py-2 border border-gray-300 rounded-lg bg-white text-gray-800 placeholder-gray-400 focus:outline-none focus:ring-2 focus:ring-red-500"
			/>
			<input 
				type="password" 
				bind:value={password} 
				placeholder="Senha" 
				class="w-full px-4 py-2 border border-gray-300 rounded-lg bg-white text-gray-800 placeholder-gray-400 focus:outline-none focus:ring-2 focus:ring-red-500"
			/>
		</form>

		<button 
			onclick={validation} 
			type="submit" 
			class="w-full py-2 text-white bg-red-500 hover:bg-red-600 rounded-lg font-medium transition duration-200"
		>
			Entrar
		</button>

		{#if info.message == 'invalido'}
			<h1 class="text-center text-sm text-red-600 font-medium">Usuário inválido</h1>
		{/if}
	</div>
</div>
