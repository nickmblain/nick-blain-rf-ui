<script>
  import rfLogo from '../assets/rf-logo.png';
  import eventLogo from '../assets/event-logo.png';
  import SearchIcon from './icons/SearchIcon.svelte';

  let { open = false, onClose = () => {} } = $props();

  const navSections = [
    { id: 'guide', label: 'Guide', active: false, children: [] },
    {
      id: 'attendees',
      label: 'Attendees',
      active: true,
      children: ['Attendees', 'Attendee types', 'Packages', 'Reg codes', 'Discounts'],
    },
    { id: 'content', label: 'Content', active: false, children: [] },
    { id: 'exhibitors', label: 'Exhibitors', active: false, children: [] },
  ];
</script>

<div class="sidebar__scrim" class:sidebar__scrim--visible={open} onclick={onClose} aria-hidden="true"></div>

<aside class="sidebar" class:sidebar--open={open}>
  <div class="sidebar__rail">
    <div class="sidebar__rail-top">
      <div class="sidebar__rail-logo-wrap">
        <img class="sidebar__rail-logo" src={rfLogo} alt="RainFocus" />
      </div>
      <button class="sidebar__rail-event" type="button" aria-label="RainFocus Summit">
        <img class="sidebar__rail-event-icon" src={eventLogo} alt="" />
      </button>
    </div>
    <div class="sidebar__rail-bottom">
      <span class="sidebar__avatar">FL</span>
    </div>
  </div>

  <div class="sidebar__panel">
    <div class="sidebar__header">
      <h1 class="sidebar__event-name">RainFocus Summit</h1>
    </div>
    <p class="sidebar__event-meta">Lehi, UT&nbsp;&bull;&nbsp;December 15th</p>

    <div class="sidebar__search-wrap">
      <label class="sidebar__search">
        <span class="sidebar__search-icon"><SearchIcon /></span>
        <input class="sidebar__search-input" type="search" placeholder="Search" />
      </label>
    </div>

    <nav class="sidebar__nav">
      {#each navSections as section (section.id)}
        <div class="sidebar__nav-group">
          <div class="sidebar__nav-item" class:sidebar__nav-item--active={section.active}>
            <span class="sidebar__nav-dot" class:sidebar__nav-dot--active={section.active}></span>
            <span class="sidebar__nav-label">{section.label}</span>
          </div>
          {#if section.children.length}
            <ul class="sidebar__nav-children">
              {#each section.children as child (child)}
                <li class="sidebar__nav-child">{child}</li>
              {/each}
            </ul>
          {/if}
        </div>
      {/each}
    </nav>
  </div>
</aside>

<style lang="scss">
  @use '../styles/variables' as *;

  .sidebar {
    display: flex;
    flex-shrink: 0;
    align-self: stretch;
    min-height: 100vh;
    background: $color-bg;
    border-right: 1px solid $color-border;

    &__scrim {
      display: none;
    }

    &__rail {
      width: $rail-width;
      flex-shrink: 0;
      display: flex;
      flex-direction: column;
      align-items: center;
      padding: 12px 7.5px;
      border-right: 1px solid $color-border;
    }

    &__rail-top {
      display: flex;
      flex-direction: column;
      align-items: center;
    }

    &__rail-logo-wrap {
      display: flex;
      align-items: center;
      justify-content: center;
      width: 100%;
    }

    &__rail-logo {
      width: 100%;
      height: auto;
      display: block;
    }

    &__rail-event {
      display: flex;
      align-items: center;
      justify-content: center;
      padding: 4px 8px 8px;
    }

    &__rail-event-icon {
      width: $rail-icon-size;
      height: $rail-icon-size;
      border-radius: $radius-sm;
      object-fit: cover;
    }

    &__rail-bottom {
      position: fixed;
      left: 0;
      bottom: 16px;
      width: $rail-width;
      display: flex;
      align-items: center;
      justify-content: center;
      z-index: 60;
    }

    &__panel {
      width: calc(#{$sidebar-width} - #{$rail-width});
      flex-shrink: 0;
      display: flex;
      flex-direction: column;
      padding: 12px 8px;
      overflow-y: auto;
    }

    &__header {
      padding: 8px 8px 12px;
    }

    &__event-name {
      font-size: 14px;
      font-weight: 600;
      color: $color-text-header;
      line-height: 1.3;
    }

    &__event-meta {
      padding: 0 8px 12px;
      font-size: 10px;
      font-weight: 500;
      color: #000;
    }

    &__search-wrap {
      padding: 0 8px 12px;
      width: 100%;
    }

    &__search {
      display: flex;
      align-items: center;
      gap: 4px;
      padding: 4px;
      border-radius: $radius-sm;
      background: $color-search-bg;
      width: 100%;
    }

    &__search-icon {
      display: flex;
      width: 16px;
      height: 16px;
      color: $color-search-text;
    }

    &__search-input {
      border: none;
      outline: none;
      width: 100%;
      font-size: 10px;
      font-weight: 600;
      background: transparent;
      color: $color-text-header;

      &::placeholder {
        color: $color-search-text;
      }
    }

    &__nav {
      flex: 1;
    }

    &__nav-item {
      display: flex;
      align-items: center;
      gap: 12px;
      padding: 8px 12px;
      border-radius: $radius-md;
      font-size: 14px;
      font-weight: 600;
      color: $color-text-muted;

      &--active {
        background: $color-purple-active-bg;
        color: $color-purple;
      }
    }

    &__nav-dot {
      width: $nav-dot-size;
      height: $nav-dot-size;
      border-radius: 50%;
      background: $color-text-muted;
      flex-shrink: 0;

      &--active {
        background: $color-purple;
      }
    }

    &__nav-children {
      display: flex;
      flex-direction: column;
      padding: 4px 12px 12px 48px;
    }

    &__nav-child {
      padding: 8px 0;
      font-size: 14px;
      font-weight: 600;
      color: $color-text-muted;
      cursor: pointer;

      &:hover {
        color: $color-text-header;
      }
    }

    &__avatar {
      display: inline-flex;
      align-items: center;
      justify-content: center;
      width: $rail-icon-size;
      height: $rail-icon-size;
      border-radius: 50%;
      background: $color-avatar-bg;
      color: #fff;
      font-size: 16px;
      font-weight: 400;
      flex-shrink: 0;
    }
  }

  @media (max-width: $bp-tablet) {
    .sidebar {
      position: fixed;
      inset: 0 auto 0 0;
      z-index: 50;
      transform: translateX(-100%);
      transition: transform 0.25s ease;
      box-shadow: $shadow-popover;

      &--open {
        transform: translateX(0);
      }
    }

    .sidebar__scrim {
      display: block;
      position: fixed;
      inset: 0;
      background: rgba(15, 15, 20, 0.4);
      z-index: 40;
      opacity: 0;
      pointer-events: none;
      transition: opacity 0.25s ease;

      &--visible {
        opacity: 1;
        pointer-events: auto;
      }
    }
  }

  @media (max-width: $bp-mobile) {
    .sidebar__panel {
      width: 240px;
    }
  }
</style>
