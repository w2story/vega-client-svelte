<script lang="ts">
  import type { Memo } from '../../model/memo/Memo';
  import { MemoService } from '../../service/MemoService';

  export let memo: Memo;

  $: userName = memo.userName;
  $: userIcon = memo.userIcon;
  $: body = memo.body;
  $: link = parseLink(body);
  $: regMonth = memo.regDate.getMonth() + 1;
  $: regDate = memo.regDate.getDate();

  function onLinkClick() {}

  function onClearClick() {
    MemoService.setCurrentMemo(null);
  }

  function parseLink(body: string): string {
    const match = /https?\:\/\/[^\s]+/.exec(body);
    return match ? match[0] : '';
  }
</script>

<div class="memo-content">
  <div class="bg" on:click|self={onClearClick} />
  <div class="memo-box">
    <div class="title">
      <div class="user-img">
        <img src={userIcon} alt={userName} />
      </div>
      <div class="title-txt">
        <h3>{userName}</h3>
        <p>{regMonth}월 {regDate}일</p>
      </div>
      <div class="content">
        <p class="text">{body}</p>
        {#if link}
          <!-- svelte-ignore a11y-missing-attribute -->
          <a class="link" on:click={onLinkClick}>
            <p>{link}</p>
          </a>
        {/if}
      </div>
    </div>
    <button class="modal-close" on:click={onClearClick}>
      <i class="fas fa-times" />
    </button>
  </div>
  {#if false}
    <!-- svelte-ignore a11y-missing-attribute -->
    <a class="left-btn btn">
      <i class="material-icons">navigate_before</i>
    </a>
    <!-- svelte-ignore a11y-missing-attribute -->
    <a class="right-btn btn">
      <i class="material-icons">navigate_next</i>
    </a>
  {/if}
</div>

<style lang="scss">
  .memo-content {
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
      width: 30%;
      height: 500px;
      position: absolute;
      left: 0;
      right: 0;
      top: 70px;
      margin: auto;
      border-radius: 2px;

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

        p {
          width: 100%;
          padding: 10px 5px;
          padding-bottom: 0px;
          margin-bottom: 0px;

          font-size: 14px;
          line-height: 18px;
        }

        .link {
          cursor: pointer;
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

    a.btn {
      position: absolute;
      top: calc(50% - 20px);
      margin: auto;
      cursor: pointer;

      z-index: 120;

      i {
        font-size: 40px;
      }

      &.left-btn {
        left: 0px;
      }
      &.right-btn {
        right: 0px;
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
    .memo-content {
      .memo-box {
        width: 100%;
        height: calc(100% - 50px);
        top: 50px;
      }
    }
    //CSS
  }
</style>
