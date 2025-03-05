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

	async function listarCameras() {
		try {
			cameras = await Html5Qrcode.getCameras();
			if (cameras.length > 0) {
				iniciarScanner(); // Inicia o scanner após listar as câmeras
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

<button class="cursor-pointer" on:click={iniciarLeitura}>
	Ler QRCode para realizar o pedido
</button>

<div>
	<div id="reader" style="width: 300px;"></div>
	{#if cameras.length > 1}
		<button on:click={changeCam}>Alternar Câmera</button>
	{/if}
</div>

{#if qrCode === 'true'}
	<p class="bg-green-500">QRCode validado</p>
{:else if qrCode === 'false'}
	<p class="bg-red-500">QRCode inválido</p>
{/if}
