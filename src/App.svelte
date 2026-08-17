<script>
  import Sidebar from './lib/Sidebar.svelte';
  import MainContent from './lib/MainContent.svelte';

  let sidebarOpen = $state(false);
  let activeNavId = $state('attendees');

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
</script>

<div class="app">
  <Sidebar
    open={sidebarOpen}
    onClose={() => (sidebarOpen = false)}
    activeId={activeNavId}
    onNavigate={handleNavigate}
  />
  <MainContent onMenuClick={() => (sidebarOpen = true)} />
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
