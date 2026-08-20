<script>
  import { onMount } from 'svelte';
  import Sidebar from './lib/Sidebar.svelte';
  import MainContent from './lib/MainContent.svelte';
  import EditEventModal from './lib/EditEventModal.svelte';
  import defaultEventLogo from './assets/event-logo.png';

  const EVENT_STORAGE_KEY = 'rf-ui:event';

  function loadEvent() {
    try {
      const raw = localStorage.getItem(EVENT_STORAGE_KEY);
      if (raw) return JSON.parse(raw);
    } catch {
      // corrupt or unavailable storage, fall back to the default event details
    }
    return {
      name: 'RainFocus Summit',
      date: 'December 15th',
      location: 'Lehi, UT',
      logo: defaultEventLogo,
    };
  }

  let sidebarOpen = $state(false);
  let activeNavId = $state('guide');
  let editButtonVisible = $state(true);
  let event = $state(loadEvent());
  let isEditEventOpen = $state(false);

  $effect(() => {
    try {
      localStorage.setItem(EVENT_STORAGE_KEY, JSON.stringify(event));
    } catch {
      // storage unavailable (private browsing, quota, etc.), fail silently
    }
  });

  function openEditEvent() {
    isEditEventOpen = true;
  }

  function closeEditEvent() {
    isEditEventOpen = false;
  }

  /** @param {{ name: string, date: string, location: string, logo: string }} updated */
  function saveEvent(updated) {
    event = updated;
    closeEditEvent();
  }

  /**
   * @param {string} id
   * @param {string} [anchor]
   */
  function handleNavigate(id, anchor) {
    activeNavId = id;
    if (anchor) {
      document.getElementById(anchor)?.scrollIntoView({ behavior: 'smooth', block: 'start' });
    }
  }

  onMount(() => {
    /** @type {[string, string][]} */
    const sectionOrder = [
      ['guide-intro', 'guide'],
      ['guide-module', 'attendees'],
      ['section-content', 'content'],
      ['section-exhibitors', 'exhibitors'],
    ];

    const sections = sectionOrder
      .map(([id, navId]) => ({ navId, el: document.getElementById(id) }))
      .filter((section) => section.el);

    const ACTIVATION_LINE = 120;

    function updateActiveSection() {
      const scrolledToBottom =
        window.innerHeight + window.scrollY >= document.documentElement.scrollHeight - 2;
      if (scrolledToBottom) {
        activeNavId = sections[sections.length - 1].navId;
        return;
      }

      let current = sections[0]?.navId ?? activeNavId;
      for (const section of sections) {
        if (section.el.getBoundingClientRect().top <= ACTIVATION_LINE) {
          current = section.navId;
        }
      }
      activeNavId = current;
    }

    updateActiveSection();
    window.addEventListener('scroll', updateActiveSection, { passive: true });
    window.addEventListener('resize', updateActiveSection);

    return () => {
      window.removeEventListener('scroll', updateActiveSection);
      window.removeEventListener('resize', updateActiveSection);
    };
  });
</script>

<div class="app">
  <Sidebar
    open={sidebarOpen}
    onClose={() => (sidebarOpen = false)}
    activeId={activeNavId}
    onNavigate={handleNavigate}
    showCompactEdit={!editButtonVisible}
    {event}
    onEditEvent={openEditEvent}
  />
  <MainContent
    {event}
    onMenuClick={() => (sidebarOpen = true)}
    onEditVisibilityChange={(visible) => (editButtonVisible = visible)}
    onEditEvent={openEditEvent}
  />
  <EditEventModal open={isEditEventOpen} {event} onClose={closeEditEvent} onSave={saveEvent} />
  <p class="credit">Authored by Nick Blain</p>
</div>

<style lang="scss">
  @use './styles/variables' as *;

  .app {
    display: flex;
    min-height: 100vh;
    width: 100%;
  }

  .credit {
    position: fixed;
    right: 12px;
    bottom: 8px;
    z-index: 60;
    font-size: 11px;
    color: $color-text-muted;
    opacity: 0.65;
    pointer-events: none;
    user-select: none;
  }
</style>
