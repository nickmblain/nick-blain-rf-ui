<script>
  import { scale } from 'svelte/transition';
  import eventLogo from '../assets/event-logo.png';
  import SearchIcon from './icons/SearchIcon.svelte';
  import EditIcon from './icons/EditIcon.svelte';
  import RfLogoIcon from './icons/RfLogoIcon.svelte';

  /**
   * @typedef {{ id: string, label: string, anchor?: string }} NavChild
   * @typedef {{ id: string, label: string, anchor: string, children: NavChild[] }} NavSection
   */

  /**
   * @typedef {object} Props
   * @property {boolean} [open]
   * @property {() => void} [onClose]
   * @property {string} [activeId]
   * @property {(id: string, anchor?: string) => void} [onNavigate]
   * @property {boolean} [showCompactEdit]
   */

  /** @type {Props} */
  let {
    open = false,
    onClose = () => {},
    activeId = 'attendees',
    onNavigate = () => {},
    showCompactEdit = false,
  } = $props();

  /** @type {NavSection[]} */
  const navSections = [
    { id: 'guide', label: 'Guide', anchor: 'section-guide', children: [] },
    {
      id: 'attendees',
      label: 'Attendees',
      anchor: 'guide-module',
      children: [
        { id: 'attendees-list', label: 'Attendees', anchor: 'guide-module' },
        { id: 'attendee-types', label: 'Attendee types', anchor: 'guide-step-1' },
        { id: 'packages', label: 'Packages' },
        { id: 'reg-codes', label: 'Reg codes' },
        { id: 'discounts', label: 'Discounts' },
      ],
    },
    { id: 'content', label: 'Content', anchor: 'section-content', children: [] },
    { id: 'exhibitors', label: 'Exhibitors', anchor: 'section-exhibitors', children: [] },
  ];

  let query = $state('');

  /**
   * @param {NavSection[]} sections
   * @param {string} needle
   * @returns {NavSection[]}
   */
  function filterSections(sections, needle) {
    const q = needle.trim().toLowerCase();
    if (!q) return sections;
    return sections
      .map((section) => {
        if (section.label.toLowerCase().includes(q)) return section;
        const matchingChildren = section.children.filter((child) => child.label.toLowerCase().includes(q));
        if (matchingChildren.length) return { ...section, children: matchingChildren };
        return null;
      })
      .filter((section) => section !== null);
  }

  let filteredSections = $derived(filterSections(navSections, query));

  /** @param {NavSection} section */
  function selectSection(section) {
    onNavigate(section.id, section.anchor);
    onClose();
  }

  /**
   * @param {NavSection} section
   * @param {NavChild} child
   */
  function selectChild(section, child) {
    onNavigate(section.id, child.anchor);
    onClose();
  }
</script>

<div class="sidebar__scrim" class:sidebar__scrim--visible={open} onclick={onClose} aria-hidden="true"></div>

<aside class="sidebar" class:sidebar--open={open}>
  <div class="sidebar__rail">
    <div class="sidebar__rail-top">
      <div class="sidebar__rail-logo-wrap">
        <span class="sidebar__rail-logo" aria-label="RainFocus"><RfLogoIcon /></span>
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
      {#if showCompactEdit}
        <button
          class="sidebar__edit-btn"
          type="button"
          aria-label="Edit event"
          transition:scale={{ duration: 180, start: 0.5 }}
        >
          <EditIcon />
        </button>
      {/if}
    </div>
    <p class="sidebar__event-meta">Lehi, UT&nbsp;&bull;&nbsp;December 15th</p>

    <div class="sidebar__search-wrap">
      <label class="sidebar__search">
        <span class="sidebar__search-icon"><SearchIcon /></span>
        <input class="sidebar__search-input" type="search" placeholder="Search" bind:value={query} />
      </label>
    </div>

    <nav class="sidebar__nav">
      {#each filteredSections as section (section.id)}
        <div class="sidebar__nav-group">
          <button
            class="sidebar__nav-item"
            class:sidebar__nav-item--active={section.id === activeId}
            type="button"
            onclick={() => selectSection(section)}
          >
            <span class="sidebar__nav-dot" class:sidebar__nav-dot--active={section.id === activeId}></span>
            <span class="sidebar__nav-label">{section.label}</span>
          </button>
          {#if section.children.length}
            <ul class="sidebar__nav-children">
              {#each section.children as child (child.id)}
                <li>
                  <button class="sidebar__nav-child" type="button" onclick={() => selectChild(section, child)}>
                    {child.label}
                  </button>
                </li>
              {/each}
            </ul>
          {/if}
        </div>
      {:else}
        <p class="sidebar__nav-empty">No results for &ldquo;{query}&rdquo;.</p>
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
      position: sticky;
      top: 0;
      height: 100vh;
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
      padding: 8px 12px 24px;
    }

    &__rail-logo {
      display: flex;
      width: 24px;
      flex-shrink: 0;

      :global(svg) {
        width: 100%;
        height: auto;
      }
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
      position: sticky;
      top: 0;
      height: 100vh;
      overflow-y: auto;
    }

    &__header {
      display: flex;
      align-items: center;
      justify-content: space-between;
      gap: 8px;
      min-height: 24px;
      padding: 8px 8px 12px;
    }

    &__event-name {
      font-size: 14px;
      font-weight: 600;
      color: $color-text-header;
      line-height: 24px;
    }

    &__edit-btn {
      display: flex;
      align-items: center;
      justify-content: center;
      width: 24px;
      height: 24px;
      border-radius: $radius-sm;
      color: $color-purple;
      background: $color-purple-active-bg;
      flex-shrink: 0;

      &:hover {
        background: rgba(92, 0, 220, 0.16);
      }

      :global(svg) {
        width: 13px;
        height: 13px;
      }
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

    &__nav-empty {
      padding: 8px 12px;
      font-size: 13px;
      color: $color-text-muted;
    }

    &__nav-item {
      width: 100%;
      display: flex;
      align-items: center;
      gap: 12px;
      padding: 8px 12px;
      border-radius: $radius-md;
      font-size: 14px;
      font-weight: 600;
      color: $color-text-muted;
      text-align: left;

      &:hover {
        background: $color-search-bg;
      }

      &--active {
        background: $color-purple-active-bg;
        color: $color-purple;

        &:hover {
          background: $color-purple-active-bg;
        }
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
      width: 100%;
      display: block;
      padding: 8px 0;
      font-size: 14px;
      font-weight: 600;
      color: $color-text-muted;
      text-align: left;
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
