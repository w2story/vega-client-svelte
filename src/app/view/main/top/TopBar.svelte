<script lang="ts">
  import { ProfileService } from '../../../service/ProfileService';
  import { WindowService } from '../../../service/WindowService';
  import PushList from '../push/PushList.svelte';
  import { PushListService } from '../push/PushListService';
  import SettingMenu from './menu/SettingMenu.svelte';

  let isPushListShow = false;
  let settingMenuShow = false;
  let profileIcon = '';
  let pushCount = 0;
  $: settingMenuActiveClass = settingMenuShow ? 'active' : 'deactive';

  ProfileService.profileIcon.subscribe((icon) => {
    profileIcon = icon;
  });

  WindowService.settingMenuShow.subscribe((v) => (settingMenuShow = v));
  PushListService.params.subscribe(
    (it) => (pushCount = it.filter((param) => !param.notification.read).length)
  );

  const toggleSideBar = () => {
    WindowService.toggleSideBar();
  };

  const toggleSettingMenu = () => {
    WindowService.settingMenuShow.update((v) => !v);
  };
</script>

<nav>
  <div class="part">
    <button class="menu-btn" on:click={toggleSideBar}>
      <img class="logo" src="/assets/image/main/main-logo-60.png" alt="logo" />
    </button>
  </div>
  <div class="part right">
    <button
      class="alert-btn"
      on:click={(_) => (isPushListShow = !isPushListShow)}
    >
      <i class="fas fa-bell" />
      {#if pushCount > 0}
        <div class="push">{pushCount}</div>
      {/if}
    </button>
    <button on:click={toggleSettingMenu} on:contextmenu|preventDefault>
      {#if profileIcon}
        <img class="profile" src={profileIcon} alt="profile" />
      {/if}
    </button>
  </div>
</nav>

<div class={'setting-menu ' + settingMenuActiveClass}><SettingMenu /></div>
{#if isPushListShow}
  <div class="push-list"><PushList /></div>
{/if}

<style lang="scss">
  $icon-size: 40px;

  nav {
    display: flex;
    width: 100%;
    height: 100%;
  }

  div.part {
    display: flex;
    width: 50%;
    padding: 5px;
    align-items: center;

    button {
      position: relative;
      width: $icon-size;
      height: $icon-size;
      border-radius: 50%;
      border: none;
      outline: none;
      cursor: pointer;
      margin: 0;
      padding: 0;
      color: var(--primary-foreground-color);
      background-color: var(--primary-background-color);

      &:hover {
        background-color: var(--primary-hoverground-color);
      }

      &:active {
        background-color: var(--primary-hoverground-color);
      }
      &.alert-btn {
        margin-right: 10px;

        i {
          font-size: 20px;
        }

        div.push {
          position: absolute;
          right: 0px;
          bottom: 0px;
          font-size: 12px;
          width: 18px;
          height: 18px;
          border-radius: 15px;
          text-align: center;
          line-height: 18px;
          background: var(--primary-activeground-color);
          color: var(--primary-activeground-font-color);
        }
      }
      &.menu-btn {
        margin-left: 10px;
      }
    }
  }

  div.part.right {
    justify-content: flex-end;
  }

  img.logo {
    width: 40px;
    height: 40px;
    cursor: pointer;
    border-radius: 20px;

    // 로고 레이어 80
    z-index: 80;
  }

  img.profile {
    width: 40px;
    height: 40px;
    border-radius: 20px;
  }

  .setting-menu {
    position: absolute;
    top: 50px;
    right: 0;
    width: 250px;
    height: auto;
    overflow: hidden;
    box-shadow: var(--primary-setting-shadow);
    transition: transform 0.25s ease-in-out;

    &.active {
      transform: translateX(0);
    }

    &.deactive {
      transform: translateX(100%);
    }
  }

  .push-list {
    position: fixed;
    right: 50px;
    overflow-y: hidden;

    -webkit-transition: max-height 0.5s ease-in-out;
    -moz-transition: max-height 0.5s ease-in-out;
    -o-transition: max-height 0.5s ease-in-out;
    transition: max-height 0.5s ease-in-out;

    &.active {
      max-height: 500px;
    }

    &.deactive {
      max-height: 0px;
    }
  }

  @media screen and (max-width: 768px) {
    .push-list {
      width: 100%;
      right: 0px;
    }
  }
</style>
