<script lang="ts">
  import type { Photo } from '../../model/photo/Photo';
  import { PhotoContentService } from '../../service/PhotoContentService';
  import { PhotoAdultFilterCommand } from './PhotoAdultFilterCommand';
  import { PhotoShareCommand } from './PhotoShareCommand';

  const filter = new PhotoAdultFilterCommand();
  const share = new PhotoShareCommand();

  export let photo: Photo;
  export let originalHeight = 0;
  export let originalWidth = 0;
  export let adult = photo.isForAdult;
  export let thumbnail = '';

  let animated: boolean = false;

  function onClick() {
    PhotoContentService.setCurrentPhoto(photo);
  }

  async function onFilterClick() {
    const nextAdult = !adult;
    const result = await filter.execute(photo.hash, nextAdult);
    if (result) {
      adult = nextAdult;
    }
  }

  function onLinkClick() {
    share.execute(photo.url);
  }
</script>

<div class="ph-img">
  <!-- svelte-ignore a11y-missing-attribute -->
  <img class:blurEffect={adult} src={thumbnail} on:click={onClick} />
  <div class="ph-info">
    <div class="bg" />
    <div class="img-wh">
      <i class="material-icons">crop_original</i>
      <p>{originalWidth}x{originalHeight}</p>
    </div>
    <div class="ph-btn-group">
      <!-- svelte-ignore a11y-missing-attribute -->
      <a class="ph-btn warning">
        <i class="material-icons" on:click={onFilterClick}>warning</i>
      </a>
      <!-- svelte-ignore a11y-missing-attribute -->
      <a class="ph-btn link">
        <i class="material-icons" on:click={onLinkClick}>link</i>
      </a>
    </div>
  </div>
  {#if adult}
    <div class="img-chk attention-limit">
      <!-- svelte-ignore a11y-missing-attribute -->
      <img class="logo" src="/assets/image/photo/img_19_limit.png" />
    </div>
  {/if}
  {#if animated}
    <div class="img-chk gif-chk">
      <!-- svelte-ignore a11y-missing-attribute -->
      <img class="logo" src="/assets/image/photo/img_gif.png" />
    </div>
  {/if}
</div>

<style lang="scss">
  .ph-img {
    width: auto;
    height: 250px;
    position: relative;
    margin: 0px 10px;
    margin-bottom: 15px;

    background-color: var(--primary-hoverground-color);
    box-shadow: 0px 2px 1px -1px rgba(0, 0, 0, 0.2),
      0px 1px 1px 0px rgba(0, 0, 0, 0.14), 0px 1px 3px 0px rgba(0, 0, 0, 0.12);

    overflow: hidden;

    &:hover {
      img.blurEffect {
        filter: blur(0px);
        -webkit-filter: blur(0px);
      }
    }

    img {
      height: 250px;
    }

    img.blurEffect {
      filter: blur(10px);
      -webkit-filter: blur(10px);
    }

    .ph-info {
      width: 100%;
      height: 40px;
      padding-top: 20px;
      display: none;

      position: absolute;
      left: 0px;
      bottom: 0px;

      overflow: hidden;

      background: #1a1a1a;
      background: -webkit-gradient(
        linear,
        left top,
        left bottom,
        from(transparent),
        to(rgba(0, 0, 0, 0.75))
      );
      background: linear-gradient(transparent, rgba(0, 0, 0, 0.75));

      .img-wh {
        width: auto;
        height: 30px;
        padding: 5px;
        float: left;

        i {
          width: auto;
          font-size: 28px;
          line-height: 30px;
          float: left;
          color: #ffffff;
          text-shadow: 0px 2px 1px -1px rgba(0, 0, 0, 0.2),
            0px 1px 1px 0px rgba(0, 0, 0, 0.14),
            0px 1px 3px 0px rgba(0, 0, 0, 0.12);
        }

        p {
          width: auto;
          float: left;
          color: #ffffff;
          font-size: 16px;
          line-height: 20px;
          padding-left: 5px;
          padding-top: 3px;
          margin: 0%;
          text-shadow: 0px 2px 1px -1px rgba(0, 0, 0, 0.2),
            0px 1px 1px 0px rgba(0, 0, 0, 0.14),
            0px 1px 3px 0px rgba(0, 0, 0, 0.12);
        }
      }

      .ph-btn-group {
        width: auto;
        height: 30px;
        padding: 5px;
        float: right;

        .ph-btn {
          width: 30px;
          height: 30px;
          font-size: 28px;
          line-height: 30px;
          cursor: pointer;
          padding: 0px 5px;
          color: #ffffff;
          text-shadow: 0px 2px 1px -1px rgba(0, 0, 0, 0.2),
            0px 1px 1px 0px rgba(0, 0, 0, 0.14),
            0px 1px 3px 0px rgba(0, 0, 0, 0.12);
        }
      }
    }

    &:hover {
      .ph-info {
        display: block;
      }
    }

    .img-chk {
      width: 30px;
      height: 22px;
      padding: 2px 3px;
      padding-right: 7px;

      position: absolute;
      left: 0px;
      top: 5px;

      background-color: #fff;
      background-color: var(--mdc-theme-surface, #fff);
      opacity: 0.9;

      border-top-right-radius: 2px;
      border-bottom-right-radius: 2px;

      box-shadow: 0px 2px 1px -1px rgba(0, 0, 0, 0.2),
        0px 1px 1px 0px rgba(0, 0, 0, 0.14), 0px 1px 3px 0px rgba(0, 0, 0, 0.12);

      img {
        width: 20px;
        height: 10px;
        padding: 3px;
      }
    }
    .img-chk.attention-limit {
      background-color: var(--primary-activeground-color);
    }
    .attention-limit + .gif-chk {
      top: 30px;
    }
  }

  @media screen and (max-width: 768px) {
    .ph-img {
      width: calc(25% - 11px);
      height: 75px;
      background: var(--primary-hoverground-color);
      overflow: hidden;
      display: flex;
      align-items: center;
      justify-content: center;
      margin: 5px 5px;

      img {
        width: 100%;
        height: auto;
        display: block;
        margin: 0px auto;
      }
      .ph-info {
        display: none;
      }
      &:hover {
        .ph-info {
          display: none;
        }
      }
    }
  }
</style>
