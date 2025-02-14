<script lang="ts">
  import { RegisterStreamCommand } from '../../model/stream/RegisterStreamCommand';
  import { StageStreamCommand } from '../../model/stream/StageStreamCommand';
  import { ToastService } from '../../service/ToastService';
  import { WindowService } from '../../service/WindowService';
  import InlineSvg from '../../view-framework/InlineSvg.svelte';

  const platforms = [
    {
      id: 'twitch',
      name: '트위치',
      icon: 'twitch',
    },
    {
      id: 'afreeca',
      name: '아프리카',
      icon: 'afreecatv',
    },
    {
      id: 'youtube',
      name: '유튜브',
      icon: 'youtube',
    },
  ];
  let currentPlatform: Platform;
  let stagedStream = null;
  let searching = false;
  let adding = false;
  let searchKeyword = '';

  function onPlatformClick(platform: Platform) {
    currentPlatform = platform;
  }

  async function onSearchClick() {
    searching = true;
    try {
      const command = new StageStreamCommand(currentPlatform.id, searchKeyword);
      const result = await command.execute();
      if (result === null) {
        ToastService.toastText('검색결과가 없습니다.');
        return;
      }
      stagedStream = result;
    } finally {
      searching = false;
    }
  }

  async function onAddClick() {
    if (!stagedStream) {
      ToastService.toastText('비정상 접근입니다.');
      return;
    }

    this.adding = true;
    const command = new RegisterStreamCommand(
      stagedStream.platform,
      stagedStream.keyId
    );
    try {
      const result = await command.execute();
      if (result) {
        ToastService.toastText('방송을 추가했습니다.');
      } else {
        ToastService.toastText('방송추가를 실패했습니다.');
      }
    } finally {
      this.adding = false;
      WindowService.closeModal();
    }
  }

  export const close = () => {
    WindowService.closeModal();
  };

  type Platform = { id: string; name: string };
</script>

<div class="modal">
  <div class="title">
    <div class="icon">
      <i class="fas fa-satellite-dish" />
    </div>
    <h3>스트리머 추가</h3>
  </div>
  {#if stagedStream === null}
    <div class="live-stream-chk">
      <div class="live-corp-list">
        {#each platforms as platform}
          <!-- svelte-ignore a11y-click-events-have-key-events -->
          <div class="live-corp" on:click={(_) => onPlatformClick(platform)}>
            <div class="live-corp-img">
              <div class="checks">
                <input
                  type="radio"
                  name="live-corp-select"
                  checked={platform === currentPlatform}
                />
                <div class="icon">
                  <InlineSvg src="/assets/image/stream/{platform.icon}.svg" />
                </div>
              </div>
            </div>
            <p>{platform.name}</p>
          </div>
        {/each}
      </div>
      <div class="live-stream-search">
        <!-- svelte-ignore a11y-label-has-associated-control -->
        <label>
          <p>방송ID검색</p>
        </label>
        <input
          id="name"
          maxlength="60"
          type="text"
          bind:value={searchKeyword}
        />
        <p class="error-up">닉네임을 입력하세요.</p>
      </div>
      <button
        class="live-stream-btn"
        disabled={searching}
        on:click={onSearchClick}
      >
        {searching ? '검색 중' : '검색'}
      </button>
    </div>
  {:else}
    <div>
      <div class="live-stream-list">
        <div class="live-stream">
          <!-- svelte-ignore a11y-missing-attribute -->
          <img src={stagedStream?.icon} />
          <div class="stream-info">
            <h3>
              <span>{stagedStream?.title} </span>
              <small>({stagedStream?.keyId})</small>
            </h3>
            <p>방송플랫폼 : {stagedStream?.platform}</p>
          </div>
        </div>
      </div>
      <button
        class="live-stream-add-btn"
        disabled={adding}
        on:click={onAddClick}
      >
        스트리머 추가
      </button>
    </div>
  {/if}
  <button class="modal-close" on:click={close}>
    <i class="fas fa-times" />
  </button>
</div>

<style lang="scss">
  .modal {
    position: relative;
    width: 480px;
    height: auto;
    max-width: 90%;
    max-height: 90%;
    background: var(--primary-background-color);
    border-radius: 2px;
    box-shadow: 0 0 8px 0 rgba(0, 0, 0, 0.08), 0 0 15px 0 rgba(0, 0, 0, 0.02),
      0 0 20px 4px rgba(0, 0, 0, 0.06);

    * {
      padding: 0%;
      margin: 0%;
    }

    .title {
      width: calc(100% - 30px);
      height: auto;
      padding: 5px 15px;
      display: flex;
      margin-bottom: 15px;
      .icon {
        width: 46px;
        height: 46px;
        color: var(--primary-foreground-color);
        margin: 0%;
        text-align: center;
        i {
          font-size: 24px;
          padding: 11px 11px;
        }
      }
      h3 {
        margin: 0%;
        width: auto;
        color: var(--primary-foreground-color);
        padding: 14px 10px;
        padding-right: 0px;
        font-size: 20px;
        height: auto;
      }
    }
    .live-corp-list {
      width: calc(100% - 20px);
      padding: 10px;
      display: inline-block;

      .live-corp {
        width: 20%;
        margin: 0% 2.5%;
        height: auto;
        float: left;

        text-align: center;

        .live-corp-img {
          width: 100%;
          height: 80px;
          background: var(--primary-hoverground-color);
          border-radius: 5px;
          overflow: hidden;
          text-align: center;
          position: relative;
          border: 1px solid var(--primary-hoverground-color);

          padding: 10px 0px;

          margin-bottom: 5px;

          .icon {
            width: 60px;
            height: 60px;
            margin-bottom: 15px;
            position: absolute;
            top: 15px;
            left: -50px;
            fill: var(--primary-foreground-color);
          }

          .checks {
            width: 21px;
            height: 21px;

            position: absolute;
            top: 5px;
            right: 5px;
          }

          &:hover {
            border-color: var(--primary-activeground-color);
          }
          &:active {
            background-color: var(--primary-activeground-color);
          }
        }

        p {
          font-size: 14px;
          color: var(--primary-foreground-color);
        }
      }
    }
    .live-stream-search {
      width: calc(100% - 20px);
      padding-bottom: 5px;
      padding-top: 10px;

      label {
        width: 100%;
        font-size: 14px;
        color: var(--primary-foreground-color);
        padding-left: 10px;

        p {
          padding: 0 10px;
          margin-bottom: 5px;
        }
      }

      input {
        width: calc(100% - 20px);
        height: 40px;
        font-size: 16px;
        margin: 0 10px;
        padding: 0px 10px;
        color: var(--primary-foreground-color);

        background-color: var(--primary-hoverground-color);
        border: 1px solid var(--primary-hoverground-color);
        border-radius: 5px;
        overflow: hidden;
      }

      p.error-up {
        display: none;
      }
    }

    button.live-stream-btn {
      width: calc(100% - 20px);
      height: 50px;
      margin: 0px 10px;
      margin-bottom: 15px;

      margin-top: 15px;
      border-radius: 2px;
      color: #ffffff;
      background-color: var(--primary-activeground-color);
      border: 1px solid var(--primary-activeground-color);

      font-size: 20px;
      font-weight: bolder;

      &:hover {
        background-color: #e91e63;
        border-color: #e91e63;
      }

      &:active {
        background-color: #d81b60;
        border-color: #d81b60;
      }

      &:disabled {
        background-color: var(--primary-borderground-color);
        border-color: var(--primary-borderground-color);
      }
    }

    .live-stream-list {
      width: calc(100% - 20px);
      padding-bottom: 5px;

      border-top: 1px solid var(--primary-borderground-color);

      display: inline-block;
      margin: 0px 10px;

      &:first-child {
        border-top: 0px;
      }

      .live-stream {
        width: 100%;
        height: 80px;
        display: inline-block;

        img {
          width: 60px;
          height: 60px;
          float: left;
          margin: 10px;
        }
        .stream-info {
          width: calc(100% - 100px);
          height: 60px;
          padding: 10px 0px;
          margin-right: 20px;
          float: left;

          h3 {
            padding-top: 15px;
            color: var(--primary-foreground-color);
          }
          p {
            font-size: 12px;
            color: var(--primary-foreground-color);
          }
        }
      }
    }

    button.live-stream-add-btn {
      width: 100%;
      height: 60px;

      margin-top: 15px;
      border-bottom-right-radius: 5px;
      border-bottom-left-radius: 5px;
      color: #ffffff;
      background-color: #ff4081;
      border: 1px solid #ff4081;

      font-size: 20px;
      font-weight: bolder;

      &:hover {
        background-color: #e91e63;
        border-color: #e91e63;
      }

      &:active {
        background-color: #d81b60;
        border-color: #d81b60;
      }

      &:disabled {
        background-color: #bdbdbd;
        border-color: #757575;
      }
    }

    .modal-close {
      width: 58px;
      height: 58px;
      position: absolute;
      top: 0%;
      right: 0%;

      background-color: var(--primary-hoverground-color);
      border: 1px solid var(--primary-hoverground-color);
      border-radius: 0%;
      text-align: center;

      i {
        font-size: 24px;
        line-height: 42px;
        padding-left: 2px;
        color: var(--primary-foreground-color);
      }

      &:hover {
        background-color: var(--primary-activeground-color);
        border: 1px solid var(--primary-activeground-color);

        i {
          color: var(--primary-activeground-font-color);
        }
      }

      &:active {
        background-color: var(--primary-activeground-color);
        border-color: var(--primary-activeground-color);

        i {
          color: var(--primary-activeground-font-color);
        }
      }
    }
  }
</style>
