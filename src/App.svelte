<script lang="ts">
  import QRScanner from './lib/QRScanner.svelte'
  import AutoCloseModal from './lib/AutoCloseModal.svelte'

  let showModal = false;
  let modalData = {
    type: "success",
    message: "-",
  }
  function handleCloseModal(event) {
    console.log("clsoe modal")
    showModal = false;
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
    } catch (error) {
      modalData = {
        type: "error",
        message: `Invalid QR Code, please try again.`,
      }
      showModal = true;
    }
	}

</script>

<main>
  <h1>Please Scan your Membership Card</h1>

  <div class="video">
    <QRScanner on:qrCodeText={handleQrCodeText}/>
  </div>

  <AutoCloseModal {showModal} {...modalData} on:closeModal={handleCloseModal}>
    <span class="modal-title">{modalData.message}</span>
  </AutoCloseModal>
</main>

<style>
  main {
    width: 100svw;
    height: 95svh;
    display: flex;
    flex-direction: column;
  }
  h1 {
    font-size: 2em;;
  }
  span.modal-title {
    font-size: 10vmin;
    font-weight: 600;
  }
  div.video {
    flex-grow: 1;
  }
</style>
