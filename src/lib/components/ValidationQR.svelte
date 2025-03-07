<script>
	import { onDestroy } from 'svelte';
	import { Html5Qrcode } from 'html5-qrcode';
	import { qrCodeValidated } from './store.js';
	import QRUsers from '$lib/mock/QRUsers.json';

	let scanner;
	let result = '';
	const senhaCorreta = QRUsers.password;
	let qrCode = '';
	let cameras = [];
	let currentCameraIndex = 0;
	let showScannerPopup = false;

	async function listarCameras() {
		try {
			cameras = await Html5Qrcode.getCameras();
			if (cameras.length > 0) {
				iniciarScanner();
			} else {
				result = 'Nenhuma câmera encontrada!';
			}
		} catch (error) {
			console.error('Erro ao listar câmeras:', error);
			result = 'Erro ao listar câmeras!';
		}
	}

	async function iniciarScanner() {
		try {
			if (cameras.length === 0) {
				await listarCameras();
				return;
			}

			const selectedCamera = cameras[currentCameraIndex].id;

			if (scanner) {
				await scanner.stop();
			}

			scanner = new Html5Qrcode('reader');
			await scanner.start(
				selectedCamera,
				{ fps: 10, qrbox: 250 },
				(decodedText) => {
					result = decodedText;
					if (result === senhaCorreta) {
						qrCode = 'true';
						qrCodeValidated.set(true);
						scanner.stop();
						showScannerPopup = false;
					} else {
						qrCode = 'false';
					}
				},
				(errorMessage) => {
					console.log(errorMessage);
				}
			);
		} catch (error) {
			console.error('Erro ao acessar a câmera:', error);
			result = 'Erro ao acessar a câmera!';
		}
	}

	function iniciarLeitura() {
		listarCameras();
		showScannerPopup = true;
	}

	function changeCam() {
		if (cameras.length > 1) {
			currentCameraIndex = (currentCameraIndex + 1) % cameras.length;
			iniciarScanner();
		}
	}

	onDestroy(() => {
		if (scanner) {
			scanner.stop().catch((err) => console.warn('Erro ao parar scanner:', err));
		}
	});
</script>

<div class="rounded-lg bg-green-400 p-3 text-sm text-white">
	<button class="cursor-pointer" on:click={iniciarLeitura}>
		Ler QRCode para realizar o pedido
	</button>
</div>
{#if showScannerPopup}
	<div class="bg-opacity-50 fixed inset-0 z-50 flex items-center justify-center bg-black">
		<div class="mr-5 ml-5 w-96 rounded-lg bg-white p-6 shadow-lg">
			<div id="reader" style="width: 300px; "></div>

			{#if cameras.length > 1}
				<button class="mb-4 w-full rounded-md bg-red-500 px-4 py-2 text-white" on:click={changeCam}
					>Alternar Câmera</button
				>
			{/if}

			{#if qrCode === 'true'}
				<p class="rounded-md bg-green-500 p-2 text-white">QRCode validado</p>
			{:else if qrCode === 'false'}
				<p class="rounded-md bg-red-500 p-2 text-white">QRCode inválido</p>
			{/if}

			<button class="mt-4 text-red-500" on:click={() => (showScannerPopup = false)}>Fechar</button>
		</div>
	</div>
{/if}
