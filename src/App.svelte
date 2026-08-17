<script>
  import Sidebar from './lib/Sidebar.svelte';
  import MainContent from './lib/MainContent.svelte';

  let sidebarOpen = $state(false);
  let currentPage = $state('guide');
  let activeNavId = $state('attendees');

  /**
   * @param {string} page
   * @param {string} id
   */
  function handleNavigate(page, id) {
    currentPage = page;
    activeNavId = id;
  }
</script>

<div class="app">
  <Sidebar
    open={sidebarOpen}
    onClose={() => (sidebarOpen = false)}
    activeId={activeNavId}
    onNavigate={handleNavigate}
  />
  <MainContent onMenuClick={() => (sidebarOpen = true)} page={currentPage} />
</div>

<style lang="scss">
  .app {
    display: flex;
    min-height: 100vh;
    width: 100%;
  }
</style>
