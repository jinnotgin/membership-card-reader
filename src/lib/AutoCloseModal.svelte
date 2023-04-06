<script>
	import { createEventDispatcher } from 'svelte';
	const dispatch = createEventDispatcher();

  export let showModal = false;
  export let type = "success";
  export let message = "No message";

  function close() {
    console.log("closing modal")
    dispatch('closeModal');
  }

  let timeout_showModal;
  $: {
    if (showModal === true) timeout_showModal = setTimeout(close, 3000);
  }
</script>

<div>
  {#if showModal}
    <div class="modal" on:click|self={close}>
      <div class="modal-content" class:success={type === "success"} class:error={type === "error"} on:click|stopPropagation={close}>
        <slot />
      </div>
    </div>
  {/if}
</div>

<style>
  .modal {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .modal-content {
    background-color: white;
    padding: 2rem;
    border-radius: 0.25rem;
    color: black;
  }

  .modal-content.success {
    background-color: teal;
    color: white;
  }

  .modal-content.error {
    background-color: salmon;
    color: white;
  }
</style>