<script>
  let { title = '' } = $props();
</script>

<section class="coming-soon">
  <h2 class="coming-soon__heading">{title}</h2>
  <p class="coming-soon__description">This section hasn't been built out yet — check back soon.</p>
</section>

<style lang="scss">
  @use '../../styles/variables' as *;

  .coming-soon {
    display: flex;
    flex-direction: column;
    gap: 16px;
    padding-top: 24px;
    border-top: 1px solid $color-border;

    &__heading {
      font-size: 24px;
      font-weight: 600;
      color: $color-text-header;
    }

    &__description {
      font-size: 16px;
      font-weight: 400;
      line-height: 24px;
      color: $color-text-body;
    }
  }
</style>
