<script lang="ts">
  export let fullscreen = false;

	import jsQR from 'jsqr';
	import { onMount, createEventDispatcher } from 'svelte';
	const dispatch = createEventDispatcher();

	let video;
	let canvas;
  const CONSTANTS = {
    NO_PERMISSION: "NO_PERMISSION",
    PENDING_PERMISSION: "PENDING_PERMISSION",
    HAS_PERMISSION: "HAS_PERMISSION",
  };
  let status = CONSTANTS.PENDING_PERMISSION;

	onMount(() => {
		navigator.mediaDevices.getUserMedia({ video: true })
			.then(function(stream) {
        status = CONSTANTS.HAS_PERMISSION;
				video.srcObject = stream;
				video.play();
				requestAnimationFrame(captureFrame);
			})
			.catch(function(err) {
        status = CONSTANTS.NO_PERMISSION;
				console.log('Error: ' + err);
			});
	});

	function captureFrame() {
		const ctx = canvas.getContext('2d');
		ctx.drawImage(video, 0, 0, canvas.width, canvas.height);
		
		const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);

		const qrCode = jsQR(imageData.data, imageData.width, imageData.height);
		if (qrCode) {
			const qrCodeText = qrCode.data;
			dispatch('qrCodeText', { text: qrCodeText });
		}
		requestAnimationFrame(captureFrame);
	}
</script>

<div class="qrScanner">
  <video class:fullscreen bind:this={video} autoplay></video>
  <canvas bind:this={canvas} style="display:none;"></canvas>
	<div class="overlay">
		<div class="dottedBox"></div>
		{#if status === CONSTANTS.NO_PERMISSION}
    	<p class="instructions">❌ Error: No camera permission given.</p>
		{:else if status === CONSTANTS.PENDING_PERMISSION}
			<p class="instructions">🧐 Waiting for user to provide camera permission...</p>
		{:else}
			<p class="instructions">Align QR Code within frame to scan</p>
		{/if}
	</div>
</div>

<style>
	.qrScanner {
		text-align: center;
    position: relative;
	}
	.qrScanner, video,canvas {
		height: 100%;
		width: auto;
	}
	video.fullscreen {
		position: fixed;
		top: 0;
		left: 0;
		min-width: 100%;
		min-height: 100%;
		object-fit: cover;
		z-index: -1;
	}
	.overlay {
		position: absolute;
		inset: 0;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
    z-index: 2;
	}
	.overlay .dottedBox {
		margin-top: 3%;
		border: 5px dotted white;
		border-radius: 1em;
		height: 65%;
		aspect-ratio: 1;
    box-shadow: 0 0 0 100vmax rgba(0, 0, 0, 0.7);
    z-index: 3;
	}
	.instructions {
		margin-top: 3%;
		color: white;
		font-size: 2rem;
		font-weight: 600;
    z-index: 3;
	}
</style>