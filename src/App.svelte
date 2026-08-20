<script>
  import { onMount } from 'svelte';
  import Sidebar from './lib/Sidebar.svelte';
  import MainContent from './lib/MainContent.svelte';

  let sidebarOpen = $state(false);
  let activeNavId = $state('guide');
  let editButtonVisible = $state(true);

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
  />
  <MainContent
    onMenuClick={() => (sidebarOpen = true)}
    onEditVisibilityChange={(visible) => (editButtonVisible = visible)}
  />
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
