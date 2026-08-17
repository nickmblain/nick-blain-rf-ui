<script>
  import { onMount } from 'svelte';
  import { fade } from 'svelte/transition';
  import eventLogo from '../assets/event-logo.png';
  import MenuIcon from './icons/MenuIcon.svelte';
  import EditIcon from './icons/EditIcon.svelte';
  import GuideSection from './sections/GuideSection.svelte';
  import ExhibitorsSection from './sections/ExhibitorsSection.svelte';
  import ComingSoonSection from './sections/ComingSoonSection.svelte';

  let { onMenuClick = () => {}, onEditVisibilityChange = () => {} } = $props();

  /** @type {HTMLButtonElement | undefined} */
  let editBtnEl = $state();
  let headerVisible = $state(true);

  onMount(() => {
    if (!editBtnEl) return;
    const observer = new IntersectionObserver(
      ([entry]) => {
        headerVisible = entry.isIntersecting;
        onEditVisibilityChange(entry.isIntersecting);
      },
      { threshold: 0 },
    );
    observer.observe(editBtnEl);
    return () => observer.disconnect();
  });
</script>

<main class="main">
  <div class="main__topbar">
    <button class="main__menu-btn" type="button" onclick={onMenuClick} aria-label="Open navigation">
      <MenuIcon />
    </button>
    {#if !headerVisible}
      <div class="main__topbar-compact" transition:fade={{ duration: 150 }}>
        <img class="main__topbar-compact-logo" src={eventLogo} alt="" />
        <div class="main__topbar-compact-info">
          <span class="main__topbar-compact-title">RainFocus Summit</span>
          <span class="main__topbar-compact-meta">December 15th</span>
          <span class="main__topbar-compact-meta">Lehi, Utah</span>
        </div>
        <button class="main__topbar-compact-edit" type="button" aria-label="Edit event">
          <EditIcon />
        </button>
      </div>
    {/if}
  </div>

  <div class="main__body">
    <div class="main__event-header">
      <img class="main__event-logo" src={eventLogo} alt="RainFocus Summit logo" />
      <div class="main__event-info">
        <h1 class="main__event-title">RainFocus Summit</h1>
        <div class="main__event-meta">
          <p class="main__event-date">December 15th</p>
          <p class="main__event-location">Lehi, Utah</p>
        </div>
      </div>
      <button bind:this={editBtnEl} class="main__edit-btn" type="button">Edit event</button>
    </div>

    <div id="section-guide" class="main__section">
      <GuideSection />
    </div>

    <div id="section-content" class="main__section">
      <ComingSoonSection title="Content" />
    </div>

    <div id="section-exhibitors" class="main__section">
      <ExhibitorsSection />
    </div>
  </div>
</main>

<style lang="scss">
  @use 'sass:color';
  @use '../styles/variables' as *;

  .main {
    flex: 1;
    min-width: 0;

    &__topbar {
      display: none;
    }

    &__menu-btn {
      width: 36px;
      height: 36px;
      align-items: center;
      justify-content: center;
      border-radius: $radius-sm;
      color: $color-text-header;
    }

    &__body {
      display: flex;
      flex-direction: column;
      gap: 40px;
      padding: 40px 48px;
    }

    &__edit-btn {
      background: $color-purple;
      color: #fff;
      font-weight: 700;
      font-size: 14px;
      padding: 8px 16px;
      border-radius: $radius-sm;
      flex-shrink: 0;
      transition: background 0.15s ease;

      &:hover {
        background: color.adjust($color-purple, $lightness: -8%);
      }
    }

    &__event-header {
      display: flex;
      align-items: flex-start;
      gap: 16px;
    }

    &__event-logo {
      width: $event-logo-size;
      height: $event-logo-size;
      border-radius: $radius-md;
      object-fit: cover;
      flex-shrink: 0;
    }

    &__event-info {
      display: flex;
      flex-direction: column;
      gap: 8px;
      flex: 1;
      min-width: 0;
    }

    &__event-title {
      font-size: 32px;
      font-weight: 300;
      color: $color-text-header;
    }

    &__event-meta {
      display: flex;
      flex-direction: column;
      font-size: 16px;
      font-weight: 400;
      line-height: 24px;
      color: $color-text-body;
    }

    &__section {
      display: flex;
      flex-direction: column;
      gap: 40px;
      scroll-margin-top: 24px;
    }
  }

  @media (max-width: $bp-tablet) {
    .main {
      &__topbar {
        display: flex;
        align-items: center;
        gap: 12px;
        padding: 12px 16px;
        border-bottom: 1px solid $color-border;
        position: fixed;
        top: 0;
        left: 0;
        right: 0;
        z-index: 30;
        background: $color-bg;
      }

      &__topbar-compact {
        display: flex;
        align-items: center;
        gap: 10px;
        flex: 1;
        min-width: 0;
      }

      &__topbar-compact-logo {
        width: 32px;
        height: 32px;
        border-radius: $radius-sm;
        object-fit: cover;
        flex-shrink: 0;
      }

      &__topbar-compact-info {
        display: flex;
        flex-direction: column;
        flex: 1;
        min-width: 0;
      }

      &__topbar-compact-title {
        font-size: 14px;
        font-weight: 600;
        color: $color-text-header;
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
      }

      &__topbar-compact-meta {
        font-size: 11px;
        font-weight: 400;
        line-height: 1.3;
        color: $color-text-body;
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
      }

      &__topbar-compact-edit {
        display: flex;
        align-items: center;
        justify-content: center;
        width: 28px;
        height: 28px;
        border-radius: $radius-sm;
        color: $color-purple;
        background: $color-purple-active-bg;
        flex-shrink: 0;

        &:hover {
          background: rgba(92, 0, 220, 0.16);
        }

        :global(svg) {
          width: 14px;
          height: 14px;
        }
      }

      &__menu-btn {
        display: inline-flex;
        flex-shrink: 0;
      }

      &__body {
        padding: 76px 24px 48px;
        gap: 28px;
      }

      &__event-header {
        flex-wrap: wrap;
      }

      &__edit-btn {
        order: 1;
      }
    }
  }

  @media (max-width: $bp-mobile) {
    .main__body {
      padding: 76px 16px 40px;
      gap: 24px;
    }

    .main {
      &__event-header {
        gap: 14px;
      }

      &__event-logo {
        width: 56px;
        height: 56px;
      }

      &__event-title {
        font-size: 22px;
      }
    }
  }
</style>
