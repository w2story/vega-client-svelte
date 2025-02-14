<script lang="ts">
  import { onMount } from 'svelte';
  import { get } from 'svelte/store';
  import type { SocketMyStatus } from '../../model/socket/common/SocketModel';
  import { ChatNetworkService } from '../../service/ChatNetworkService';
  import { CheckerNetworkService } from '../../service/CheckerNetworkService';
  import { OptionService } from '../../service/OptionService';
  import { ProfileService } from '../../service/ProfileService';
  import { WindowService } from '../../service/WindowService';
  import ChatPage from '../chat/ChatPage.svelte';
  import ModalLayer from '../modal/ModalLayer.svelte';
  import PopupContentLayer from '../popup/PopupContentLayer.svelte';
  import BottomBar from './bottom/BottomBar.svelte';
  import ContentView from './content/ContentView.svelte';
  import ImagePopupLayer from './popup/ImagePopupLayer.svelte';
  import SideBar from './side/SideBar.svelte';
  import TopBar from './top/TopBar.svelte';
  import VerticalSplitView from './VerticalSplitView.svelte';

  export let privateKey: string;

  let sideBarVisible = get(WindowService.sideBarShow);
  let isCheckerBarEnable = get(OptionService.enableCheckerBar);
  let windowInnerWidth: number;
  let chatViewOffset = get(OptionService.chatViewOffset);
  let chatConnected = false;

  ChatNetworkService.init(privateKey);
  CheckerNetworkService.init(privateKey);
  WindowService.sideBarShow.subscribe((v) => (sideBarVisible = v));
  OptionService.enableCheckerBar.subscribe((v) => (isCheckerBarEnable = v));
  ChatNetworkService.isConnected.subscribe((it) => (chatConnected = it));

  ProfileService.loadStreamProfile(privateKey);

  onMount(() => {
    ChatNetworkService.applyMyStatusEvent.subscribe((it) => {
      it && onApplyMyStatusEvent(it);
    });
  });

  function onOffsetChanged(e: CustomEvent<number>) {
    OptionService.setChatViewOffset(e.detail);
  }

  function onPhotoClick() {
    WindowService.openContent({ type: 'photo' });
    WindowService.closeSideBar();
  }

  function onDonationClick() {
    console.log('donation-click');

    WindowService.openContent({ type: 'donation' });
    WindowService.closeSideBar();
  }

  function onMemoClick() {
    WindowService.openContent({ type: 'memo' });
    WindowService.closeSideBar();
  }

  function onConnectClick() {
    ChatNetworkService.init(privateKey);
  }

  function onApplyMyStatusEvent(e: SocketMyStatus) {
    ProfileService.statusMessage.set(e.statusMessage);
    ProfileService.profileIcon.set(e.icon);
    ProfileService.nickname.set(e.nickname);
  }
</script>

<div class="main-section">
  <VerticalSplitView
    minSideSize={300}
    offset={chatViewOffset}
    on:offsetchange={onOffsetChanged}
  >
    <div slot="side" class="chat-section">
      {#if chatConnected}
        <ChatPage />
      {:else}
        <div class="error-page">
          <div class="container">
            <div>Not Connected</div>
            <button on:click={onConnectClick}>connect</button>
          </div>
        </div>
      {/if}
    </div>
    <div
      slot="main"
      class="content-section"
      class:checker-attached={isCheckerBarEnable}
    >
      <div class="content"><ContentView /></div>
      <div class="bottom-bar"><BottomBar /></div>
    </div>
  </VerticalSplitView>
  <div class="side-bar" class:show={sideBarVisible}>
    <SideBar
      chatEnabled={false}
      on:photoclick={onPhotoClick}
      on:memoclick={onMemoClick}
      on:donationclick={onDonationClick}
    />
  </div>
</div>
<div class="top-bar"><TopBar /></div>

<PopupContentLayer />
<ModalLayer />
<ImagePopupLayer />

<svelte:window bind:innerWidth={windowInnerWidth} />

<style lang="scss">
  $top-bar-height: 50px;
  $side-bar-width: 250px;
  $default-chat-width: 300px;
  $dark-mode-bg-color: var(--primary-background-color);
  $dark-mode-font-color: var(--primary-foreground-color);
  $dark-mode-active-color: var(--primary-hoverground-color);

  .top-bar {
    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
    height: $top-bar-height;
    margin: 0;
    z-index: 2;
    background: var(--primary-background-color);
    box-shadow: var(--primary-box-shadow);
  }

  .main-section {
    position: absolute;
    left: 0;
    top: $top-bar-height;
    display: block;
    width: 100%;
    height: calc(100% - #{$top-bar-height});
    background-color: var(--primary-hoverground-color);

    .chat-section {
      width: 100%;
      height: 100%;
      background: var(--primary-background-color);
      box-shadow: var(--primary-chat-shadow);
    }

    .content-section {
      $bottom-bar-height: 60px;
      position: absolute;
      width: 100%;
      height: 100%;
      background: var(--primary-hoverground-color);

      .content {
        position: absolute;
        left: 0;
        top: 0;

        width: 100%;
        height: 100%;
      }

      .bottom-bar {
        display: none;
        position: absolute;
        left: 0;
        bottom: 0;
        width: 100%;
        height: 0px;
      }

      &.checker-attached {
        .content {
          height: calc(100% - #{$bottom-bar-height});
        }
        .bottom-bar {
          display: block;
          height: $bottom-bar-height;
        }
      }
    }

    .side-bar {
      position: absolute;
      left: 0;
      top: 0;
      transition: 0.2s ease-in-out;
      width: $side-bar-width;
      height: 100%;
      transform: translateX(-$side-bar-width);
      z-index: 5;

      &.show {
        transform: none;
      }
    }
  }

  .error-page {
    width: 100%;
    height: 100%;
    display: flex;
    color: var(--primary-foreground-color);
    align-items: center;

    .container {
      width: 100%;
      text-align: center;
    }
  }
</style>
