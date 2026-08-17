<script>
  import personPortalIcon from '../../assets/person-portal.png';
  import WorkflowArrowIcon from '../icons/WorkflowArrowIcon.svelte';
  import PlusIcon from '../icons/PlusIcon.svelte';
  import LaptopIcon from '../icons/LaptopIcon.svelte';

  const workflows = [
    { id: 1, title: 'Attendee Registration', description: 'Start by creating a general registration workflow' },
    { id: 2, title: 'Attendee Registration', description: 'Start by creating a general registration workflow' },
    { id: 3, title: 'Attendee Registration', description: 'Start by creating a general registration workflow' },
  ];
</script>

<section class="guide">
  <h2 class="guide__heading">Event setup guide</h2>
  <p class="guide__description">
    See the available list of modules below. We suggest that you start with the attendee module.
  </p>
</section>

<div class="guide__module" id="guide-module">
  <div class="guide__module-header">
    <img class="guide__module-icon" src={personPortalIcon} alt="" />
    <h3 class="guide__module-title">Attendee</h3>
  </div>

  <div class="guide__step" id="guide-step-1">
    <p class="guide__step-label"><strong>Step 1:</strong> Base settings.</p>
    <div class="settings-card">
      <div class="settings-card__col">
        <h4 class="settings-card__title">General</h4>
        <p class="settings-card__desc">Define Attendee types &amp; attributes</p>
      </div>
      <div class="settings-card__col">
        <h4 class="settings-card__title">Title</h4>
        <p class="settings-card__desc">
          Description that explains the value goes here. Description that explains the value goes here.
        </p>
      </div>
      <div class="settings-card__col">
        <h4 class="settings-card__title">Title</h4>
        <p class="settings-card__desc">
          Description that explains the value goes here. Description that explains the value goes here.
        </p>
      </div>
    </div>
  </div>

  <div class="guide__step">
    <p class="guide__step-label"><strong>Step 2:</strong> Build registration workflows.</p>
    <div class="workflow-grid">
      {#each workflows as workflow (workflow.id)}
        <div class="workflow-card">
          <div class="workflow-card__header">
            <span class="workflow-card__icon"><WorkflowArrowIcon /></span>
            <h4 class="workflow-card__title">{workflow.title}</h4>
          </div>
          <p class="workflow-card__desc">{workflow.description}</p>
        </div>
      {/each}
      <button class="workflow-card workflow-card--add" type="button">
        <span class="workflow-card__add-icon"><PlusIcon /></span>
        <span class="workflow-card__add-label">Add Registration Workflow</span>
      </button>
    </div>
  </div>

  <div class="guide__step">
    <p class="guide__step-label"><strong>Step 3:</strong> Design post-registration experiences.</p>
    <div class="portal-card">
      <div class="portal-card__header">
        <span class="portal-card__icon"><LaptopIcon /></span>
        <h4 class="portal-card__title">Attendee Portal</h4>
      </div>
      <p class="portal-card__desc">Manage the portal that attendees will see after they've register for your event.</p>
    </div>
  </div>
</div>

<style lang="scss">
  @use '../../styles/variables' as *;

  .guide {
    display: flex;
    flex-direction: column;
    gap: 16px;

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

    &__module {
      display: flex;
      flex-direction: column;
      gap: 24px;
      padding-top: 24px;
      border-top: 1px solid $color-border;
    }

    &__module-header {
      display: flex;
      align-items: center;
      gap: 24px;
    }

    &__module-icon {
      display: block;
      width: $module-icon-size;
      height: $module-icon-size;
    }

    &__module-title {
      font-size: 20px;
      font-weight: 600;
      color: $color-text-header;
    }

    &__step {
      display: flex;
      flex-direction: column;
      gap: 8px;
    }

    &__step-label {
      font-size: 14px;
      color: $color-text-strong;

      strong {
        font-weight: 700;
        color: $color-text-header;
      }
    }
  }

  .settings-card {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 32px;
    border: 1px solid $color-border;
    border-radius: $radius-sm;
    padding: 16px 24px;
    box-shadow: $shadow-card;

    &__title {
      font-size: 13px;
      font-weight: 700;
      color: $color-text-header;
      margin-bottom: 4px;
    }

    &__desc {
      font-size: 12px;
      font-weight: 400;
      color: $color-text-body;
      line-height: normal;
    }
  }

  .workflow-grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 24px;
  }

  .workflow-card {
    text-align: left;
    display: flex;
    flex-direction: column;
    gap: 4px;
    border: 1px solid $color-border;
    border-radius: $radius-sm;
    padding: 24px 32px;
    box-shadow: $shadow-card;

    &__header {
      display: flex;
      align-items: center;
      gap: 8px;
    }

    &__icon {
      display: flex;
      width: $workflow-icon-size;
      height: $workflow-icon-size;
      color: $color-purple;
      flex-shrink: 0;
    }

    &__title {
      font-size: 13px;
      font-weight: 700;
      color: $color-text-header;
    }

    &__desc {
      font-size: 12px;
      font-weight: 400;
      color: $color-text-body;
      line-height: normal;
    }

    &--add {
      align-items: center;
      justify-content: center;
      transition: background 0.15s ease;

      &:hover {
        background: $color-purple-active-bg;
      }
    }

    &__add-icon {
      display: flex;
      width: $workflow-icon-size;
      height: $workflow-icon-size;
      color: $color-purple;
    }

    &__add-label {
      font-size: 12px;
      font-weight: 400;
      color: $color-text-header;
      text-align: center;
    }
  }

  .portal-card {
    display: flex;
    flex-direction: column;
    gap: 4px;
    border: 1px solid $color-border;
    border-radius: $radius-sm;
    padding: 24px 32px;
    box-shadow: $shadow-card;
    max-width: 460px;

    &__header {
      display: flex;
      align-items: center;
      gap: 8px;
    }

    &__icon {
      display: flex;
      width: $workflow-icon-size;
      height: $workflow-icon-size;
      color: $color-purple;
      flex-shrink: 0;
    }

    &__title {
      font-size: 13px;
      font-weight: 700;
      color: $color-text-header;
    }

    &__desc {
      font-size: 12px;
      font-weight: 400;
      color: $color-text-body;
      line-height: normal;
    }
  }

  @media (max-width: $bp-tablet) {
    .settings-card {
      grid-template-columns: 1fr;
      gap: 20px;
    }

    .workflow-grid {
      grid-template-columns: 1fr;
    }
  }

  @media (max-width: $bp-mobile) {
    .guide__heading {
      font-size: 18px;
    }

    .settings-card,
    .workflow-card,
    .portal-card {
      padding: 16px;
    }
  }
</style>
