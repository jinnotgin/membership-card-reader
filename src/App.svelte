<script lang="ts">
  import QRScanner from './lib/QRScanner.svelte'
  import AutoCloseModal from './lib/AutoCloseModal.svelte'
  let audioSuccess;
  let audioError;

  let showModal = false;
  let modalData = {
    type: "success",
    message: "You're not supposed to see this!",
  }
  function handleCloseModal(event) {
    showModal = false;
  }
  function playAudio(audioElem) {
    if (!audioElem.paused) {
      audioElem.pause();
      audioElem.currentTime = 0;
    }
    audioElem.play();
  }

	function handleQrCodeText(event) {
    if (showModal) return;

    try {
      const {name = false, id = false} = JSON.parse(event.detail.text);
      if (!name || !id) throw "Incomplete data."

      modalData = {
        type: "success",
        message: `Welcome, ${name}!`,
      }
      showModal = true;
      // playAudio("success");
      playAudio(audioSuccess);
    } catch (error) {
      modalData = {
        type: "error",
        message: `Invalid QR Code.`,
      }
      showModal = true;
      playAudio(audioError);
    }
	}

</script>

<main>
  <div class="scanner">
    <QRScanner fullscreen={true} on:qrCodeText={handleQrCodeText}/>
  </div>

  <AutoCloseModal {showModal} {...modalData} on:closeModal={handleCloseModal}>
    <span class="modal-title">{modalData.message}</span>
  </AutoCloseModal>

  <audio autoplay playsinline bind:this={audioSuccess}>
    <source src="sounds/success.mp3" type="audio/mpeg">
  </audio>
  <audio autoplay playsinline bind:this={audioError}>
    <source src="sounds/error.mp3" type="audio/mpeg">
  </audio>
</main>

<style>
  main {
    height: 100svh;
    width: 100svw;
    position: relative;
  }
  h1 {
    font-size: 2em;;
  }
  span.modal-title {
    font-size: 10vmin;
    font-weight: 600;
  }
  div.scanner {
    height: 100%;
    width: 100%;
  }
</style>
