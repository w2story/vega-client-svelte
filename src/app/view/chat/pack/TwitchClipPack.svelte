<script lang="ts">
  import { OptionService } from '../../../service/OptionService';
  import { WindowService } from '../../../service/WindowService';
  import { LinkUtils } from '../../../util/link/LinkUtils';
  import { MobileUtils } from '../../../util/mobile/MobileUtils';
  import { PopupContentService } from '../../popup/PopupContentService';

  export let body: string;
  let isDataSaveMode: boolean;
  $: json = JSON.parse(body);
  $: type = json.game;
  $: icon = json.icon;
  $: link = LinkUtils.addQuery(json.link, 'parent', location.hostname);
  $: title = json.title;
  $: thumbnail = json.thumbnail;

  OptionService.enableDataSave.subscribe((it) => (isDataSaveMode = it));

  function onClick() {
    if (MobileUtils.isMobile()) {
      WindowService.openContent({ type: 'iframe', src: link });
    } else {
      PopupContentService.addContent({
        type: 'iframe',
        src: { title: 'Twitch Clip', link },
      });
    }
  }

  function onContextMenu() {
    WindowService.openContent({
      type: 'iframe',
      src: link,
    });
  }
</script>

{#if isDataSaveMode}
  <div on:click={onClick}>(대충 트위치 클립)</div>
{:else}
  <div
    class="twitch-clip-pack"
    on:click={onClick}
    on:contextmenu|preventDefault={onContextMenu}
  >
    <img class="thumbnail" src={thumbnail} alt="{title} 썸네일" />
    <div class="logo description-added" />
    <div class="tw-str-info">
      <img class="logo" src={icon} alt="{title} 로고" />
      <div>
        <h4>{title}</h4>
        <p>{type}</p>
      </div>
    </div>
  </div>
{/if}

<style lang="scss">
  @mixin truncate {
    overflow: hidden;
    white-space: nowrap;
    text-overflow: ellipsis;
  }

  @mixin marquee($time) {
    overflow: hidden;
    white-space: nowrap;
    box-sizing: border-box;
    animation: marquee $time linear infinite;
  }

  .twitch-clip-pack {
    display: flex;
    position: relative;
    width: 230px;
    height: 130px;
    margin: 3px 0 3px 0;
    font-size: 12px;
    line-height: 15px;
    word-break: break-all;
    align-items: center;
    border-radius: 5px;
    overflow: hidden;
    cursor: default;
    color: #e8e8e8;

    &::before {
      position: absolute;
      width: 100%;
      height: 100%;
      border-radius: 5px;
      content: '';
      background: #000;
      opacity: 0.75;
    }

    &:hover {
      &::before {
        opacity: 0.6;
      }
    }

    .thumbnail {
      position: absolute;
      width: 100%;
      opacity: 0.3;
    }

    .tw-str-info {
      position: absolute;
      bottom: 0px;
      left: 0px;
      width: 100%;
      * {
        margin: 0%;
        padding: 0%;
      }
      .logo {
        width: 40px;
        height: 40px;
        margin: 5px;
        float: left;
        border-radius: 50%;
        box-shadow: 1px 1px 1px rgba($color: #000000, $alpha: 0.4);

        // background: $youtube-logo;
        &.description-added {
          bottom: 25px;
        }
      }
      div {
        width: calc(100% - 50px);
        float: left;
        margin-top: 6px;
        h4 {
          font-size: 16px;
          line-height: 18px;
          color: #ffffff;
          text-shadow: 1px 1px 1px rgba($color: #000000, $alpha: 0.4);
          white-space: nowrap;
          overflow: hidden;
          text-overflow: ellipsis;
        }
        p {
          font-size: 12px;
          line-height: 16px;
          color: #e0e0e0;
          text-shadow: 1px 1px 1px rgba($color: #000000, $alpha: 0.4);
          white-space: nowrap;
          overflow: hidden;
          text-overflow: ellipsis;
        }
      }
    }
  }
</style>
