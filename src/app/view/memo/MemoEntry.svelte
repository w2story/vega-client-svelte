<script lang="ts">
  import type { Memo } from '../../model/memo/Memo';
  import { MemoService } from '../../service/MemoService';

  export let memo: Memo;

  $: regDate = memo.regDate.toISOString();
  $: body = memo.body;
  $: author = memo.userName;

  function shareMemo() {
    MemoService.shareMemo(memo.hash);
  }

  function onClick() {
    MemoService.setCurrentMemo(memo);
  }
</script>

<div class="memo-box">
  <div class="mm-info">
    <p>{regDate}</p>
  </div>
  <pre class="mm-txt" on:click={onClick}>{body}</pre>
  <div class="mm-user">
    <p>{author} 작성</p>
  </div>
  <div class="mm-btn-group">
    <!-- svelte-ignore a11y-missing-attribute -->
    <a class="mm-btn warning">
      <i class="material-icons">warning</i>
    </a>
    <!-- svelte-ignore a11y-missing-attribute -->
    <a class="mm-btn link" on:click={shareMemo}>
      <i class="material-icons">link</i>
    </a>
  </div>
</div>

<style lang="scss">
  .memo-box {
    width: 300px;
    height: 300px;
    position: relative;
    margin: 0px 10px;
    margin-top: 35px;

    background-color: var(--primary-hoverground-color);
    color: var(--primary-foreground-color);

    border-radius: 3px;

    box-shadow: 0px 2px 1px -1px rgba(0, 0, 0, 0.2),
      0px 1px 1px 0px rgba(0, 0, 0, 0.14), 0px 1px 3px 0px rgba(0, 0, 0, 0.12);

    .mm-info {
      max-width: 50%;
      height: 30px;
      padding: 0px 10px;
      position: absolute;
      left: 10px;
      top: -14px;
      overflow: hidden;
      border-radius: 3px;

      background-color: var(--primary-borderground-color);
      color: var(--primary-foreground-color);

      box-shadow: 0px 2px 1px -1px rgba(0, 0, 0, 0.2),
        0px 1px 1px 0px rgba(0, 0, 0, 0.14), 0px 1px 3px 0px rgba(0, 0, 0, 0.12);

      text-align: center;

      p {
        font-size: 12px;
        line-height: 30px;
        margin: 0%;
        padding: 0%;
      }
    }

    .mm-txt {
      width: calc(100% - 20px);
      height: 236px;
      padding: 25px 10px;
      padding-bottom: 0px;
      margin-bottom: 0px;

      font-size: 14px;
      white-space: nowrap;
      overflow: hidden;
      text-overflow: ellipsis;

      /* 여러 줄 자르기 추가 스타일 */
      white-space: normal;
      line-height: 1.2;
      text-align: left;
      word-wrap: break-word;
      display: -webkit-box;
      -webkit-line-clamp: 14;
      -webkit-box-orient: vertical;
    }
    .mm-user {
      width: calc(100% - 103px);
      height: 30px;
      padding: 5px;
      float: left;

      p {
        font-size: 12px;
        line-height: 25px;
        margin: 0%;
        padding: 0%;
      }
    }
    .mm-btn-group {
      width: 78px;
      height: 30px;
      padding: 5px;
      float: right;

      .mm-btn {
        width: 30px;
        height: 30px;
        font-size: 28px;
        line-height: 30px;
        cursor: pointer;
        padding: 0px 5px;
        text-shadow: 0px 2px 1px -1px rgba(0, 0, 0, 0.2),
          0px 1px 1px 0px rgba(0, 0, 0, 0.14),
          0px 1px 3px 0px rgba(0, 0, 0, 0.12);
      }
    }
  }
</style>
