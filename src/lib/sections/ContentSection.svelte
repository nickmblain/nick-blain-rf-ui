<script>
  import ContentStackIcon from '../icons/ContentStackIcon.svelte';
  import EditIcon from '../icons/EditIcon.svelte';
  import PlusIcon from '../icons/PlusIcon.svelte';
  import Modal from '../Modal.svelte';

  const STORAGE_KEY = 'rf-ui:agenda';

  const seedAgenda = [
    {
      id: '1',
      time: '9:00 AM',
      title: 'Opening Keynote: The Future of Events',
      type: 'keynote',
      location: 'Main Stage',
    },
    {
      id: '2',
      time: '10:30 AM',
      title: 'Building Scalable Registration Flows',
      type: 'session',
      location: 'Track B',
    },
    {
      id: '3',
      time: '12:00 PM',
      title: 'Lunch & Networking',
      type: 'break',
      location: 'Grand Hall',
    },
    {
      id: '4',
      time: '1:30 PM',
      title: 'Panel: Scaling Exhibitor Programs',
      type: 'panel',
      location: 'Track A',
    },
    {
      id: '5',
      time: '3:00 PM',
      title: 'Closing Remarks & Happy Hour',
      type: 'networking',
      location: 'Main Stage',
    },
  ];

  const typeLabels = {
    keynote: 'Keynote',
    session: 'Session',
    panel: 'Panel',
    break: 'Break',
    networking: 'Networking',
  };

  function loadAgenda() {
    try {
      const raw = localStorage.getItem(STORAGE_KEY);
      if (raw) return JSON.parse(raw);
    } catch {
      // corrupt or unavailable storage, fall back to the seed agenda
    }
    return seedAgenda;
  }

  /** Rough sort key so freeform "9:00 AM" style times still land in day order. */
  function timeToMinutes(time) {
    const match = /^(\d{1,2}):(\d{2})\s*(AM|PM)?$/i.exec(String(time ?? '').trim());
    if (!match) return Number.MAX_SAFE_INTEGER;
    let hours = Number(match[1]) % 12;
    if (/pm/i.test(match[3] ?? '')) hours += 12;
    return hours * 60 + Number(match[2]);
  }

  let agenda = $state(loadAgenda());

  $effect(() => {
    try {
      localStorage.setItem(STORAGE_KEY, JSON.stringify(agenda));
    } catch {
      // storage unavailable (private browsing, quota, etc.), fail silently
    }
  });

  let sortedAgenda = $derived([...agenda].sort((a, b) => timeToMinutes(a.time) - timeToMinutes(b.time)));

  let isModalOpen = $state(false);
  let editingId = $state(/** @type {string | null} */ (null));
  let formTime = $state('');
  let formTitle = $state('');
  let formType = $state('session');
  let formLocation = $state('');
  let formError = $state('');

  function openAddModal() {
    editingId = null;
    formTime = '';
    formTitle = '';
    formType = 'session';
    formLocation = '';
    formError = '';
    isModalOpen = true;
  }

  /** @param {typeof seedAgenda[number]} item */
  function openEditModal(item) {
    editingId = item.id;
    formTime = item.time;
    formTitle = item.title;
    formType = item.type;
    formLocation = item.location;
    formError = '';
    isModalOpen = true;
  }

  function closeModal() {
    isModalOpen = false;
  }

  function deleteItem() {
    if (!editingId) return;
    agenda = agenda.filter((item) => item.id !== editingId);
    closeModal();
  }

  /** @param {SubmitEvent} e */
  function handleSubmit(e) {
    e.preventDefault();
    if (!formTitle.trim()) {
      formError = 'A title is required.';
      return;
    }
    if (!formTime.trim()) {
      formError = 'A time is required.';
      return;
    }

    if (editingId) {
      agenda = agenda.map((item) =>
        item.id === editingId
          ? { ...item, time: formTime.trim(), title: formTitle.trim(), type: formType, location: formLocation.trim() }
          : item,
      );
    } else {
      agenda = [
        ...agenda,
        {
          id: crypto.randomUUID ? crypto.randomUUID() : String(Date.now()),
          time: formTime.trim(),
          title: formTitle.trim(),
          type: formType,
          location: formLocation.trim() || 'TBD',
        },
      ];
    }
    closeModal();
  }
</script>

<section class="content">
  <div class="content__header">
    <span class="content__icon"><ContentStackIcon /></span>
    <h2 class="content__heading">Content</h2>
  </div>
  <p class="content__description">
    Build out the day-of schedule attendees will follow, from keynotes to breaks.
  </p>
</section>

<div class="content__module">
  <div class="content__toolbar">
    <h3 class="content__toolbar-title">Event day timeline</h3>
    <button class="content__add-btn" type="button" onclick={openAddModal}>
      <span class="content__add-icon"><PlusIcon /></span>
      Add agenda item
    </button>
  </div>

  {#if sortedAgenda.length}
    <ol class="timeline">
      {#each sortedAgenda as item (item.id)}
        <li class="timeline-item">
          <div class="timeline-item__rail">
            <span class="timeline-item__dot timeline-item__dot--{item.type}"></span>
            <span class="timeline-item__line"></span>
          </div>
          <div class="timeline-item__card">
            <div class="timeline-item__time">{item.time}</div>
            <div class="timeline-item__body">
              <div class="timeline-item__header">
                <h4 class="timeline-item__title">{item.title}</h4>
                <span class="timeline-item__type timeline-item__type--{item.type}">{typeLabels[item.type]}</span>
              </div>
              <p class="timeline-item__location">{item.location}</p>
            </div>
            <button
              class="timeline-item__edit"
              type="button"
              aria-label="Edit {item.title}"
              onclick={() => openEditModal(item)}
            >
              <EditIcon />
            </button>
          </div>
        </li>
      {/each}
    </ol>
  {:else}
    <p class="content__empty">No agenda items yet. Add the first one to build out the day.</p>
  {/if}
</div>

<Modal open={isModalOpen} title={editingId ? 'Edit agenda item' : 'Add agenda item'} onClose={closeModal}>
  <form class="agenda-form" onsubmit={handleSubmit}>
    <label class="agenda-form__field">
      <span class="agenda-form__label">Time</span>
      <input class="agenda-form__input" type="text" placeholder="e.g. 9:00 AM" bind:value={formTime} />
    </label>
    <label class="agenda-form__field">
      <span class="agenda-form__label">Title</span>
      <input class="agenda-form__input" type="text" placeholder="e.g. Closing Panel" bind:value={formTitle} />
    </label>
    <label class="agenda-form__field">
      <span class="agenda-form__label">Type</span>
      <select class="agenda-form__input" bind:value={formType}>
        <option value="keynote">Keynote</option>
        <option value="session">Session</option>
        <option value="panel">Panel</option>
        <option value="break">Break</option>
        <option value="networking">Networking</option>
      </select>
    </label>
    <label class="agenda-form__field">
      <span class="agenda-form__label">Location</span>
      <input class="agenda-form__input" type="text" placeholder="e.g. Main Stage" bind:value={formLocation} />
    </label>

    {#if formError}
      <p class="agenda-form__error">{formError}</p>
    {/if}

    <div class="agenda-form__actions">
      {#if editingId}
        <button type="button" class="agenda-form__delete" onclick={deleteItem}>Delete</button>
      {/if}
      <div class="agenda-form__actions-right">
        <button type="button" class="agenda-form__cancel" onclick={closeModal}>Cancel</button>
        <button type="submit" class="agenda-form__submit">{editingId ? 'Save changes' : 'Add agenda item'}</button>
      </div>
    </div>
  </form>
</Modal>

<style lang="scss">
  @use 'sass:color';
  @use '../../styles/variables' as *;

  .content {
    display: flex;
    flex-direction: column;
    gap: 16px;

    &__header {
      display: flex;
      align-items: center;
      gap: 16px;
    }

    &__icon {
      display: flex;
      width: $module-icon-size;
      height: $module-icon-size;
      flex-shrink: 0;

      :global(svg) {
        width: 100%;
        height: 100%;
      }
    }

    &__heading {
      font-size: 24px;
      font-weight: 600;
      color: $color-text-header;
    }

    &__description {
      font-size: 16px;
      font-weight: 400;
      line-height: 24px;
      color: $color-text-body;
    }

    &__module {
      display: flex;
      flex-direction: column;
      gap: 20px;
      padding-top: 24px;
      border-top: 1px solid $color-border;
    }

    &__toolbar {
      display: flex;
      align-items: center;
      justify-content: space-between;
      gap: 16px;
    }

    &__toolbar-title {
      font-size: 16px;
      font-weight: 700;
      color: $color-text-header;
    }

    &__add-btn {
      display: flex;
      align-items: center;
      gap: 6px;
      background: $color-purple;
      color: #fff;
      font-weight: 700;
      font-size: 14px;
      padding: 8px 16px;
      border-radius: $radius-sm;
      flex-shrink: 0;
      white-space: nowrap;
      transition: background 0.15s ease;

      &:hover {
        background: color.adjust($color-purple, $lightness: -8%);
      }
    }

    &__add-icon {
      display: flex;
      width: 12px;
      height: 12px;

      :global(svg) {
        width: 100%;
        height: 100%;
      }
    }

    &__empty {
      font-size: 14px;
      color: $color-text-muted;
    }
  }

  $timeline-colors: (
    keynote: $color-pink,
    session: $color-purple,
    panel: #b45309,
    break: $color-text-muted,
    networking: #0f9d58,
  );

  .timeline {
    display: flex;
    flex-direction: column;
  }

  .timeline-item {
    display: flex;
    gap: 16px;

    &:last-child .timeline-item__line {
      display: none;
    }

    &__rail {
      display: flex;
      flex-direction: column;
      align-items: center;
      width: 12px;
      flex-shrink: 0;
      padding-top: 6px;
    }

    &__dot {
      width: 12px;
      height: 12px;
      border-radius: 50%;
      flex-shrink: 0;

      @each $type, $color in $timeline-colors {
        &--#{$type} {
          background: $color;
        }
      }
    }

    &__line {
      flex: 1;
      width: 2px;
      background: $color-border;
      margin-top: 4px;
    }

    &__card {
      display: flex;
      align-items: flex-start;
      gap: 16px;
      flex: 1;
      padding: 16px 20px 24px;
      border-bottom: 1px solid $color-border;
    }

    &__time {
      width: 84px;
      flex-shrink: 0;
      font-size: 13px;
      font-weight: 700;
      color: $color-text-header;
      padding-top: 2px;
    }

    &__body {
      flex: 1;
      min-width: 0;
      display: flex;
      flex-direction: column;
      gap: 4px;
    }

    &__header {
      display: flex;
      align-items: center;
      flex-wrap: wrap;
      gap: 8px;
    }

    &__title {
      font-size: 14px;
      font-weight: 700;
      color: $color-text-header;
    }

    &__type {
      flex-shrink: 0;
      font-size: 11px;
      font-weight: 700;
      padding: 2px 8px;
      border-radius: 999px;
      white-space: nowrap;

      @each $type, $color in $timeline-colors {
        &--#{$type} {
          background: rgba($color, 0.12);
          color: $color;
        }
      }
    }

    &__location {
      font-size: 12px;
      font-weight: 400;
      color: $color-text-body;
    }

    &__edit {
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
        color: $color-purple;
      }

      :global(svg) {
        width: 14px;
        height: 14px;
      }
    }
  }

  .agenda-form {
    display: flex;
    flex-direction: column;
    gap: 16px;

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
      align-items: center;
      justify-content: space-between;
      gap: 12px;
      margin-top: 8px;
    }

    &__actions-right {
      display: flex;
      gap: 12px;
      margin-left: auto;
    }

    &__delete {
      padding: 8px 16px;
      font-size: 14px;
      font-weight: 700;
      color: #b91c1c;
      border-radius: $radius-sm;

      &:hover {
        background: #fef2f2;
      }
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

  @media (max-width: $bp-mobile) {
    .timeline-item {
      &__card {
        flex-wrap: wrap;
        padding: 12px 0 20px;
      }

      &__time {
        width: auto;
      }
    }
  }
</style>
