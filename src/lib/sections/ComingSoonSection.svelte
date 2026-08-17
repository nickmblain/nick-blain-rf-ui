<script>
  import RocketIcon from '../icons/RocketIcon.svelte';

  let { title = '' } = $props();
</script>

<section class="coming-soon">
  <div class="coming-soon__icon-badge">
    <RocketIcon />
  </div>
  <h2 class="coming-soon__heading">{title}</h2>
  <p class="coming-soon__description">This section hasn't been built out yet, check back soon.</p>
</section>

<style lang="scss">
  @use '../../styles/variables' as *;

  .coming-soon {
    display: flex;
    flex-direction: column;
    align-items: center;
    text-align: center;
    gap: 8px;
    max-width: 360px;
    margin: 24px auto 0;
    padding: 48px 32px;
    border: 1px solid $color-border;
    border-radius: $radius-sm;
    box-shadow: $shadow-card;

    &__icon-badge {
      display: flex;
      align-items: center;
      justify-content: center;
      width: 72px;
      height: 72px;
      border-radius: 50%;
      background: linear-gradient(135deg, $color-purple-active-bg, rgba(237, 26, 93, 0.08));
      margin-bottom: 8px;

      :global(svg) {
        width: 40px;
        height: 40px;
      }
    }

    &__heading {
      font-size: 20px;
      font-weight: 600;
      color: $color-text-header;
    }

    &__description {
      font-size: 14px;
      font-weight: 400;
      line-height: 20px;
      color: $color-text-body;
    }
  }
</style>
