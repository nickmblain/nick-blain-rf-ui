<script>
  import Modal from './Modal.svelte';

  /**
   * @typedef {{ name: string, date: string, location: string, logo: string }} EventDetails
   */

  /**
   * @typedef {object} Props
   * @property {boolean} [open]
   * @property {EventDetails} event
   * @property {() => void} [onClose]
   * @property {(event: EventDetails) => void} [onSave]
   */

  /** @type {Props} */
  let { open = false, event, onClose = () => {}, onSave = () => {} } = $props();

  let formName = $state('');
  let formDate = $state('');
  let formLocation = $state('');
  let formLogo = $state('');
  let formError = $state('');

  /** @type {HTMLInputElement | undefined} */
  let logoInputEl = $state();

  $effect(() => {
    if (!open) return;
    formName = event.name;
    formDate = event.date;
    formLocation = event.location;
    formLogo = event.logo;
    formError = '';
  });

  /** @param {Event} e */
  function handleLogoChange(e) {
    const file = /** @type {HTMLInputElement} */ (e.target).files?.[0];
    if (!file) return;
    const reader = new FileReader();
    reader.onload = () => {
      formLogo = String(reader.result);
    };
    reader.readAsDataURL(file);
  }

  /** @param {SubmitEvent} e */
  function handleSubmit(e) {
    e.preventDefault();
    if (!formName.trim()) {
      formError = 'Event name is required.';
      return;
    }
    onSave({
      name: formName.trim(),
      date: formDate.trim() || event.date,
      location: formLocation.trim() || event.location,
      logo: formLogo,
    });
  }
</script>

<Modal {open} title="Edit event" {onClose}>
  <form class="edit-event" onsubmit={handleSubmit}>
    <div class="edit-event__logo-row">
      <img class="edit-event__logo-preview" src={formLogo} alt="" />
      <div class="edit-event__logo-actions">
        <button type="button" class="edit-event__logo-btn" onclick={() => logoInputEl?.click()}>
          Change logo
        </button>
        <input
          bind:this={logoInputEl}
          class="edit-event__logo-input"
          type="file"
          accept="image/*"
          onchange={handleLogoChange}
        />
      </div>
    </div>

    <label class="edit-event__field">
      <span class="edit-event__label">Event name</span>
      <input class="edit-event__input" type="text" placeholder="Event name" bind:value={formName} />
    </label>
    <label class="edit-event__field">
      <span class="edit-event__label">Date</span>
      <input class="edit-event__input" type="text" placeholder="e.g. December 15th" bind:value={formDate} />
    </label>
    <label class="edit-event__field">
      <span class="edit-event__label">Location</span>
      <input class="edit-event__input" type="text" placeholder="e.g. Lehi, UT" bind:value={formLocation} />
    </label>

    {#if formError}
      <p class="edit-event__error">{formError}</p>
    {/if}

    <div class="edit-event__actions">
      <button type="button" class="edit-event__cancel" onclick={onClose}>Cancel</button>
      <button type="submit" class="edit-event__submit">Save changes</button>
    </div>
  </form>
</Modal>

<style lang="scss">
  @use 'sass:color';
  @use '../styles/variables' as *;

  .edit-event {
    display: flex;
    flex-direction: column;
    gap: 16px;

    &__logo-row {
      display: flex;
      align-items: center;
      gap: 16px;
    }

    &__logo-preview {
      width: 64px;
      height: 64px;
      border-radius: $radius-md;
      object-fit: cover;
      flex-shrink: 0;
      background: $color-search-bg;
    }

    &__logo-input {
      display: none;
    }

    &__logo-btn {
      padding: 8px 14px;
      font-size: 13px;
      font-weight: 700;
      color: $color-purple;
      background: $color-purple-active-bg;
      border-radius: $radius-sm;

      &:hover {
        background: rgba(92, 0, 220, 0.16);
      }
    }

    &__field {
      display: flex;
      flex-direction: column;
      gap: 4px;
    }

    &__label {
      font-size: 13px;
      font-weight: 700;
      color: $color-text-header;
    }

    &__input {
      border: 1px solid $color-border;
      border-radius: $radius-sm;
      padding: 8px 12px;
      font-size: 14px;
      color: $color-text-header;
      background: $color-bg;

      &:focus {
        outline: none;
        border-color: $color-purple;
      }
    }

    &__error {
      font-size: 13px;
      color: #b91c1c;
    }

    &__actions {
      display: flex;
      justify-content: flex-end;
      gap: 12px;
      margin-top: 8px;
    }

    &__cancel {
      padding: 8px 16px;
      font-size: 14px;
      font-weight: 700;
      color: $color-text-muted;
      border-radius: $radius-sm;

      &:hover {
        background: $color-search-bg;
      }
    }

    &__submit {
      background: $color-purple;
      color: #fff;
      font-weight: 700;
      font-size: 14px;
      padding: 8px 16px;
      border-radius: $radius-sm;
      transition: background 0.15s ease;

      &:hover {
        background: color.adjust($color-purple, $lightness: -8%);
      }
    }
  }
</style>
