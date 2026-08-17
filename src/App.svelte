<script>
  import { tick } from 'svelte';
  import Sidebar from './lib/Sidebar.svelte';
  import MainContent from './lib/MainContent.svelte';

  let sidebarOpen = $state(false);
  let currentPage = $state('guide');
  let activeNavId = $state('attendees');
  let pendingAnchor = $state(/** @type {string | null} */ (null));

  /**
   * @param {string} page
   * @param {string} id
   * @param {string} [anchor]
   */
  function handleNavigate(page, id, anchor) {
    currentPage = page;
    activeNavId = id;
    pendingAnchor = anchor ?? null;
  }

  $effect(() => {
    if (!pendingAnchor || currentPage !== 'guide') return;
    const id = pendingAnchor;
    pendingAnchor = null;
    tick().then(() => {
      document.getElementById(id)?.scrollIntoView({ behavior: 'smooth', block: 'start' });
    });
  });
</script>

<div class="app">
  <Sidebar
    open={sidebarOpen}
    onClose={() => (sidebarOpen = false)}
    activeId={activeNavId}
    onNavigate={handleNavigate}
  />
  <MainContent onMenuClick={() => (sidebarOpen = true)} page={currentPage} />
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
