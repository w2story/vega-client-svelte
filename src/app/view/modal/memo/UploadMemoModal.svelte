<script lang="ts">
  import { onMount } from 'svelte';
  import { MemoService } from '../../../service/MemoService';
  import { ProfileService } from '../../../service/ProfileService';
  import { WindowService } from '../../../service/WindowService';

  let name = 'name';
  let icon = '';
  let dateString = new Date().toISOString();
  let memo = '';

  onMount(() => {
    ProfileService.profileIcon.subscribe((it) => (icon = it));
    ProfileService.nickname.subscribe((it) => (name = it));
  });

  function close() {
    WindowService.closeModal();
  }

  async function upload() {
    await MemoService.uploadMemo(memo);
    WindowService.closeModal();
  }
</script>

<div class="memo-upload">
  <div class="bg" on:click|self={close} />
  <div class="memo-box">
    <div class="title">
      <div class="user-img">
        <img src={icon} alt={name} />
      </div>
      <div class="title-txt">
        <h3>{name}</h3>
        <p>{dateString}</p>
      </div>
      <form class="content" on:submit|preventDefault>
        <textarea
          name="memo"
          placeholder="건전한 메모장을 위해 타인에게 불쾌감을 주는 욕설 또는 위험한 링크를 제한합니다."
          bind:value={memo}
        />
        <button class="sub-btn memo-sub" on:click={upload}>메모작성</button>
      </form>
    </div>
    <button class="modal-close" on:click={close}>
      <i class="fas fa-times" />
    </button>
  </div>
</div>

<style lang="scss">
  // 메모 업로드
  .memo-upload {
    width: 100%;
    height: 100%;
    position: fixed;
    top: 0px;
    left: 0px;
    overflow-y: scroll;
    overflow-x: hidden;

    .bg {
      width: 100%;
      height: 100%;
      background-color: rgba(0, 0, 0, 0.5);
      position: fixed;
      top: 0px;
      left: 0px;
      z-index: 100;
    }
    .memo-box {
      width: 50%;
      height: 500px;
      position: absolute;
      left: 0;
      right: 0;
      top: 50px;
      margin: auto;
      border-radius: 10px;

      background-color: var(--primary-background-color);
      color: var(--primary-foreground-color);

      box-shadow: 0px 2px 1px -1px rgba(0, 0, 0, 0.2),
        0px 1px 1px 0px rgba(0, 0, 0, 0.14), 0px 1px 3px 0px rgba(0, 0, 0, 0.12);

      z-index: 120;

      .title {
        width: calc(100% - 40px);
        height: 60px;
        padding: 25px 20px;

        position: relative;

        .user-img {
          width: 60px;
          height: 60px;
          border-radius: 100%;
          overflow: hidden;

          float: left;

          img {
            width: 100%;
            height: 100%;
          }
        }
        .title-txt {
          width: calc(100% - 70px);
          margin-left: 70px;
          padding-top: 7px;
          padding-bottom: 7px;
          h3 {
            width: 100%;
            font-weight: normal;
            margin: 0%;
            padding: 0%;
            font-size: 24px;
            line-height: 30px;
          }
          p {
            width: 100%;
            font-weight: normal;
            margin: 0%;
            padding: 0%;
            font-size: 14px;
            line-height: 16px;
          }
        }
      }
      .content {
        width: 100%;

        textarea {
          width: 100%;
          margin-top: 20px;
          border: 0px;
          min-height: 300px;
          font-size: 16px;
          line-height: 20px;
          resize: none;

          background-color: var(--primary-background-color);
          color: var(--primary-foreground-color);
        }
        .sub-btn {
          width: 100%;
          height: 50px;
          border-radius: 2px;
          font-size: 20px;
          font-weight: bolder;
          letter-spacing: 3px;
          margin-top: 25px;

          background-color: var(--primary-activeground-color);
          border: 1px solid var(--primary-activeground-color);

          color: var(--primary-activeground-font-color);
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
  }

  /*
##Device = Low Resolution Tablets, Mobiles (Landscape)
##Screen = B/w 481px to 767px
##Device = 저해상도 태블릿, 모바일(가로)
##Screen = 481px에서 767px 사이
*/

  @media (min-width: 320px) and (max-width: 767px) {
    .memo-upload {
      .memo-box {
        width: 100%;
        height: calc(100% - 50px);
        top: 50px;
      }
    }
    //CSS
  }
</style>
