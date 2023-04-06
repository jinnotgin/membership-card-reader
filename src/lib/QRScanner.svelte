<script lang="ts">
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
  {#if status === CONSTANTS.NO_PERMISSION}
    <p>Error: No camera permission given.</p>
  {:else if status === CONSTANTS.PENDING_PERMISSION}
    <p>Waiting for user to provide camera permission...</p>
  {/if}
  <video bind:this={video} autoplay></video>
  <canvas bind:this={canvas} style="display:none;"></canvas>
	<div class="scanAreaOverlay">
		<div class="dottedBox"></div>
	</div>
</div>

<style>
	div.qrScanner {
		text-align: center;
    position: relative;
	}
	div.qrScanner, video,canvas {
		height: 100%;
		width: auto;
	}
	div.scanAreaOverlay {
		position: absolute;
		inset: 0;
		display: flex;
		align-items: center;
		justify-content: center;
	}
	div.scanAreaOverlay div.dottedBox {
		border: 5px dotted white;
		border-radius: 1em;
		height: 75%;
		aspect-ratio: 1;
	}
</style>