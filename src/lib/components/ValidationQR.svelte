<script>
	import { onMount, onDestroy } from 'svelte';
	import { Html5Qrcode } from 'html5-qrcode';
	import { qrCodeValidated } from './store.js';
	import QRUsers from '$lib/mock/QRUsers.json';

	let scanner;
	let result = '';
	const senhaCorreta = QRUsers.password;
	let qrCode = '';

	async function iniciarScanner() {
		try {
			const cameras = await Html5Qrcode.getCameras();
			if (cameras.length === 0) {
				result = 'Nenhuma câmera encontrada!';
				return;
			}
			let cameraId = cameras[0].id;
			scanner = new Html5Qrcode('reader');
			await scanner.start(cameraId, { fps: 10, qrbox: 250 }, (decodedText) => {
				result = decodedText;

				if (result === senhaCorreta) {
					qrCode = 'true';
					qrCodeValidated.set(true);
					scanner.stop();
				} else {
					qrCode = 'false';
				}
			});
		} catch (error) {
			console.error('Erro ao acessar a câmera:', error);
			result = 'Erro ao acessar a câmera!';
		}
	}

	onDestroy(() => {
		if (scanner) {
			scanner.stop().catch((err) => console.warn('Erro ao parar scanner:', err));
		}
	});
</script>

<button class="cursor-pointer" on:click={() => iniciarScanner()}> ler qrcode para validar</button>
<div>
	<h2>Escaneie um QR Code</h2>
	<div id="reader" style="width: 300px;"></div>
</div>
{#if qrCode === 'true'}
	<p class="bg-green-500">qrcode validado</p>
{:else if qrCode === 'false'}
	<p class="bg-red-500">qr code invalido</p>
{/if}
