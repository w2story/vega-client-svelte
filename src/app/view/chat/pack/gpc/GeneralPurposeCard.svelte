<script lang="ts">
  import { createEventDispatcher } from 'svelte';
  import { get } from 'svelte/store';
  import { OptionService } from '../../../../service/OptionService';

  export let title: string;
  export let icon: string;
  export let subtitle: string;
  export let orientation: string;
  let isDataSaveMode = get(OptionService.enableDataSave);
  const dispatch = createEventDispatcher();

  OptionService.enableDataSave.subscribe((it) => (isDataSaveMode = it));
</script>

{#if isDataSaveMode}
  <div on:click={(_) => dispatch('click')}>
    (대충 {title} / {subtitle})
  </div>
{:else}
  <div class="card-info {orientation}" on:click={(_) => dispatch('click')}>
    <div class="img">
      <img src={icon} alt="{title} 아이콘" />
    </div>
    <div class="info">
      <p class="title">{title}</p>
      <p>{subtitle}</p>
    </div>
  </div>
{/if}

<style lang="scss">
  .card-info {
    width: 100%;
    max-height: 204px;
    border-radius: 3px;
    border: 1px solid var(--primary-borderground-color);
    box-shadow: 0px 1px 1px rgba(0, 0, 0, 0.25);
    border-radius: 3px;
    overflow: hidden;
    color: var(--primary-foreground-color);

    .img {
      width: 80px;
      height: 80px;
      overflow: hidden;
      border-right: 1px solid var(--primary-borderground-color);
      background-color: var(--primary-background-color);
      margin: 0px auto;
      img {
        width: 100%;
        object-fit: cover;
      }
    }

    .info {
      height: auto;
      * {
        margin: 0%;
        padding: 0%;
        line-height: 12px;
      }
      p {
        font-size: 12px;
        padding-bottom: 4px;
        &.title {
          font-family: 'ChosunKg', sans-serif !important;
          font-size: 14px;
          line-height: 16px;
        }
      }
    }

    &.horizontal {
      width: calc(100% - 10px);
      height: 80px;
      flex-grow: 1;
      flex-shrink: 0;
      flex-basis: calc(70% - 10px);
      .img {
        width: 80px;
        height: 80px;
        float: left;
        border-right: 1px solid var(--primary-borderground-color);
      }
      .info {
        width: calc(100% - 101px);
        padding-left: 5px;
        padding-right: 15px;
        float: left;
        margin-top: 20px;
        .title {
          width: 100%;
          font-size: 16px;
          margin: 0%;
          padding-top: 10px;

          white-space: nowrap;
          overflow: hidden;
          text-overflow: ellipsis;
        }
        p {
          width: 100%;
          margin: 0%;
          padding: 0%;
          font-size: 12px;
          padding-bottom: 4px;

          white-space: nowrap;
          overflow: hidden;
          text-overflow: ellipsis;
        }
      }
    }

    &.vertical {
      width: 150px !important;
      flex-grow: 0;
      flex-shrink: 0;
      flex-basis: 100px;
      .img {
        width: 150px;
        height: 150px;
        border-bottom: 1px solid var(--primary-borderground-color);
      }
      .info {
        width: calc(100% - 6px);
        padding: 5px 3px;
        margin-top: 5px;
        overflow: hidden;
        p {
          margin-bottom: 4px;
          text-align: center;
          width: 100%;
          height: auto;
          overflow: hidden;
          text-overflow: ellipsis;
          display: -webkit-box;
          line-height: 16px;
          max-height: 32px;
          max-lines: 2;
          -webkit-line-clamp: 2; /* 표시하고자 하는 라인 수 */
          -webkit-box-orient: vertical;
        }
      }
    }
  }
</style>
