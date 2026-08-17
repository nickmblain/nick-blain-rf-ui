<script>
  import SearchIcon from '../icons/SearchIcon.svelte';

  const exhibitors = [
    { id: 1, name: 'Summit Robotics', category: 'Hardware', booth: 'A12', status: 'confirmed' },
    { id: 2, name: 'Cloudline Analytics', category: 'Software', booth: 'A14', status: 'confirmed' },
    { id: 3, name: 'Greenfield Energy', category: 'Sustainability', booth: 'B02', status: 'pending' },
    { id: 4, name: 'Northwind Logistics', category: 'Services', booth: 'B07', status: 'confirmed' },
    { id: 5, name: 'Vantage Health', category: 'Healthcare', booth: 'C01', status: 'invited' },
    { id: 6, name: 'Pixel Forge Studio', category: 'Design', booth: 'C05', status: 'pending' },
  ];

  const statusLabels = {
    confirmed: 'Confirmed',
    pending: 'Pending',
    invited: 'Invited',
  };

  let query = $state('');

  let filteredExhibitors = $derived(
    exhibitors.filter((exhibitor) => {
      const q = query.trim().toLowerCase();
      if (!q) return true;
      return exhibitor.name.toLowerCase().includes(q) || exhibitor.category.toLowerCase().includes(q);
    }),
  );
</script>

<section class="exhibitors">
  <h2 class="exhibitors__heading">Exhibitors</h2>
  <p class="exhibitors__description">
    Manage the companies exhibiting at your event, their booth assignments, and application status.
  </p>
</section>

<div class="exhibitors__module">
  <div class="exhibitors__toolbar">
    <label class="exhibitors__search">
      <span class="exhibitors__search-icon"><SearchIcon /></span>
      <input
        class="exhibitors__search-input"
        type="search"
        placeholder="Search exhibitors"
        bind:value={query}
      />
    </label>
    <button class="exhibitors__add-btn" type="button">Add Exhibitor</button>
  </div>

  {#if filteredExhibitors.length}
    <div class="exhibitor-grid">
      {#each filteredExhibitors as exhibitor (exhibitor.id)}
        <div class="exhibitor-card">
          <div class="exhibitor-card__header">
            <h3 class="exhibitor-card__name">{exhibitor.name}</h3>
            <span class="exhibitor-card__status exhibitor-card__status--{exhibitor.status}">
              {statusLabels[exhibitor.status]}
            </span>
          </div>
          <p class="exhibitor-card__category">{exhibitor.category}</p>
          <p class="exhibitor-card__booth">Booth {exhibitor.booth}</p>
        </div>
      {/each}
    </div>
  {:else}
    <p class="exhibitors__empty">No exhibitors match &ldquo;{query}&rdquo;.</p>
  {/if}
</div>

<style lang="scss">
  @use '../../styles/variables' as *;

  .exhibitors {
    display: flex;
    flex-direction: column;
    gap: 16px;

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
      gap: 24px;
      padding-top: 24px;
      border-top: 1px solid $color-border;
    }

    &__toolbar {
      display: flex;
      align-items: center;
      justify-content: space-between;
      gap: 16px;
    }

    &__search {
      display: flex;
      align-items: center;
      gap: 8px;
      padding: 8px 12px;
      border-radius: $radius-sm;
      background: $color-search-bg;
      width: 100%;
      max-width: 320px;
    }

    &__search-icon {
      display: flex;
      width: 16px;
      height: 16px;
      color: $color-search-text;
      flex-shrink: 0;
    }

    &__search-input {
      border: none;
      outline: none;
      width: 100%;
      font-size: 14px;
      background: transparent;
      color: $color-text-header;

      &::placeholder {
        color: $color-search-text;
      }
    }

    &__add-btn {
      background: $color-purple;
      color: #fff;
      font-weight: 700;
      font-size: 14px;
      padding: 8px 16px;
      border-radius: $radius-sm;
      flex-shrink: 0;
      white-space: nowrap;
    }

    &__empty {
      font-size: 14px;
      color: $color-text-muted;
    }
  }

  .exhibitor-grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 24px;
  }

  .exhibitor-card {
    display: flex;
    flex-direction: column;
    gap: 4px;
    border: 1px solid $color-border;
    border-radius: $radius-sm;
    padding: 24px;
    box-shadow: $shadow-card;

    &__header {
      display: flex;
      align-items: flex-start;
      justify-content: space-between;
      gap: 8px;
      margin-bottom: 4px;
    }

    &__name {
      font-size: 13px;
      font-weight: 700;
      color: $color-text-header;
    }

    &__status {
      flex-shrink: 0;
      font-size: 11px;
      font-weight: 700;
      padding: 2px 8px;
      border-radius: 999px;
      white-space: nowrap;

      &--confirmed {
        background: $color-purple-active-bg;
        color: $color-purple;
      }

      &--pending {
        background: #fff4e5;
        color: #b45309;
      }

      &--invited {
        background: #f0f0f0;
        color: $color-text-muted;
      }
    }

    &__category,
    &__booth {
      font-size: 12px;
      font-weight: 400;
      color: $color-text-body;
    }
  }

  @media (max-width: $bp-tablet) {
    .exhibitors__toolbar {
      flex-wrap: wrap;
    }

    .exhibitor-grid {
      grid-template-columns: 1fr;
    }
  }
</style>
