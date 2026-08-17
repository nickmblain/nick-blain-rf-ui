<script>
  import eventLogo from '../assets/event-logo.png';
  import MenuIcon from './icons/MenuIcon.svelte';
  import GuidePage from './pages/GuidePage.svelte';
  import ExhibitorsPage from './pages/ExhibitorsPage.svelte';
  import ComingSoonPage from './pages/ComingSoonPage.svelte';

  let { onMenuClick = () => {} } = $props();
</script>

<main class="main">
  <div class="main__topbar">
    <button class="main__menu-btn" type="button" onclick={onMenuClick} aria-label="Open navigation">
      <MenuIcon />
    </button>
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
      <button class="main__edit-btn" type="button">Edit event</button>
    </div>

    <div id="section-guide" class="main__section">
      <GuidePage />
    </div>

    <div id="section-content" class="main__section">
      <ComingSoonPage title="Content" />
    </div>

    <div id="section-exhibitors" class="main__section">
      <ExhibitorsPage />
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
        padding: 12px 16px;
        border-bottom: 1px solid $color-border;
      }

      &__menu-btn {
        display: inline-flex;
      }

      &__body {
        padding: 20px 24px 48px;
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
      padding: 16px 16px 40px;
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
