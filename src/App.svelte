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

    /** @type {Map<Element, string>} */
    const elementToNavId = new Map();
    const visible = new Set();

    const observer = new IntersectionObserver(
      (entries) => {
        for (const entry of entries) {
          const navId = elementToNavId.get(entry.target);
          if (!navId) continue;
          if (entry.isIntersecting) {
            visible.add(navId);
          } else {
            visible.delete(navId);
          }
        }
        for (let i = sectionOrder.length - 1; i >= 0; i--) {
          const navId = sectionOrder[i][1];
          if (visible.has(navId)) {
            activeNavId = navId;
            break;
          }
        }
      },
      { rootMargin: '0px 0px -80% 0px', threshold: 0 },
    );

    for (const [id, navId] of sectionOrder) {
      const el = document.getElementById(id);
      if (el) {
        elementToNavId.set(el, navId);
        observer.observe(el);
      }
    }

    return () => observer.disconnect();
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
