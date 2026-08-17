<script>
  import Sidebar from './lib/Sidebar.svelte';
  import MainContent from './lib/MainContent.svelte';

  let sidebarOpen = $state(false);
</script>

<div class="app">
  <Sidebar open={sidebarOpen} onClose={() => (sidebarOpen = false)} />
  <MainContent onMenuClick={() => (sidebarOpen = true)} />
</div>

<style lang="scss">
  .app {
    display: flex;
    min-height: 100vh;
    width: 100%;
  }
</style>
