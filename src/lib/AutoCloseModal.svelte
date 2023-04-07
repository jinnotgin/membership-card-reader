<script>
  import { createEventDispatcher } from 'svelte';
  const dispatch = createEventDispatcher();

  const CONSTANTS = {
    SUCCESS: "success",
    ERROR: "error",
  }

  export let showModal = false;
  export let type = CONSTANTS.SUCCESS;
  export let message = "You're not supposed to see this!";

  function close() {
    dispatch('closeModal');
  }

  let timeout_showModal;
  $: {
    if (showModal === true) timeout_showModal = setTimeout(close, 2500);
  }
</script>

<div>
  {#if showModal}
    <div class="modal" on:click|self={close}>
      <div class="modal-content" class:success={type === CONSTANTS.SUCCESS} class:error={type === CONSTANTS.ERROR} on:click|stopPropagation={close}>
        {#if type === CONSTANTS.SUCCESS}
          <svg class="icon tick" viewBox="0 0 100 100">
            <path d="M10 50 l30 30 l60 -60" stroke="white" stroke-width="10" fill="none" />
          </svg>
        {:else if type === CONSTANTS.ERROR}
          <svg class="icon cross" viewBox="0 0 100 100">
            <path d="M20,20 L80,80 M20,80 L80,20" stroke="white" stroke-width="10" />
          </svg>
        {/if}
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
    z-index: 10;
  }

  .modal-content {
    display: flex;
    flex-direction: column;
    align-items: center;
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

  svg.icon {
    height: 25%;
    max-height: 25svh;
    aspect-ratio: 1;
    margin-bottom: 2.5em;
  }
</style>