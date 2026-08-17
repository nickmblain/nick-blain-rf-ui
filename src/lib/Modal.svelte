<script>
  import { fade, scale } from 'svelte/transition';
  import CloseIcon from './icons/CloseIcon.svelte';

  let { open = false, title = '', onClose = () => {}, children } = $props();

  /** @param {MouseEvent} e */
  function handleBackdropClick(e) {
    if (e.target === e.currentTarget) onClose();
  }

  /** @param {KeyboardEvent} e */
  function handleKeydown(e) {
    if (open && e.key === 'Escape') onClose();
  }

  $effect(() => {
    if (!open) return;
    const previousOverflow = document.body.style.overflow;
    document.body.style.overflow = 'hidden';
    return () => {
      document.body.style.overflow = previousOverflow;
    };
  });
</script>

<svelte:window onkeydown={handleKeydown} />

{#if open}
  <div class="modal-backdrop" onclick={handleBackdropClick} role="presentation" transition:fade={{ duration: 150 }}>
    <div
      class="modal"
      role="dialog"
      aria-modal="true"
      aria-labelledby="modal-title"
      transition:scale={{ duration: 150, start: 0.95, opacity: 0 }}
    >
      <div class="modal__header">
        <h2 id="modal-title" class="modal__title">{title}</h2>
        <button type="button" class="modal__close" onclick={onClose} aria-label="Close">
          <CloseIcon />
        </button>
      </div>
      <div class="modal__body">
        {@render children?.()}
      </div>
    </div>
  </div>
{/if}

<style lang="scss">
  @use '../styles/variables' as *;

  .modal-backdrop {
    position: fixed;
    inset: 0;
    background: rgba(15, 15, 20, 0.45);
    z-index: 100;
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 16px;
  }

  .modal {
    width: 100%;
    max-width: 420px;
    max-height: calc(100vh - 32px);
    overflow-y: auto;
    background: $color-bg;
    border-radius: $radius-md;
    box-shadow: $shadow-popover;

    &__header {
      display: flex;
      align-items: center;
      justify-content: space-between;
      gap: 16px;
      padding: 20px 24px;
      border-bottom: 1px solid $color-border;
    }

    &__title {
      font-size: 18px;
      font-weight: 600;
      color: $color-text-header;
    }

    &__close {
      display: flex;
      align-items: center;
      justify-content: center;
      width: 28px;
      height: 28px;
      border-radius: $radius-sm;
      color: $color-text-muted;
      flex-shrink: 0;

      &:hover {
        background: $color-search-bg;
        color: $color-text-header;
      }

      :global(svg) {
        width: 14px;
        height: 14px;
      }
    }

    &__body {
      padding: 24px;
    }
  }
</style>
