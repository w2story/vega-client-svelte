<script lang="ts">
  import { MobileUtils } from '../../../../util/mobile/MobileUtils';
  import VideoButton from './VideoButton.svelte';

  export let volume: number;
  export let muted: boolean;
  export let onMuteClick = () => {};
  $: volumeIcon = getVolumeIcon(muted, volume);

  const getVolumeIcon = (muted: boolean, volume: number): string => {
    if (muted) {
      return 'mute';
    } else if (volume > 50) {
      return 'up';
    } else if (volume > 0) {
      return 'down';
    } else {
      return 'off';
    }
  };
</script>

<div class="volume-btn">
  <VideoButton icon="fas fa-volume-{volumeIcon}" onClick={onMuteClick} />
</div>
{#if !MobileUtils.isMobile()}
  {#if !muted}
    <div class="range">
      <input
        id="rangeBar"
        type="range"
        min="0"
        max="100"
        style="background: linear-gradient(to right,
      #ff4081 10%,
      #ff4081 {volume + 0}%,
      #ffffff {volume + 0}%,
      #ffffff 100%
    );"
        bind:value={volume}
      />
    </div>
  {/if}
{/if}

<style lang="scss">
  .range {
    width: 4vw;
    padding: 9px 0px;
    input[type='range'] {
      margin-left: 10px;
      -webkit-appearance: none;
      padding: 0.1em;
      width: 100%;
      height: 4px;
      cursor: pointer;
      border-radius: 12px; /* iOS */
      transition: background 450ms ease-in;
      border: 0px;
    }

    input[type='range']:focus {
      outline: none;
    }

    input[type='range']::-webkit-slider-thumb {
      -webkit-appearance: none;
      width: 16px;
      height: 16px;
      background: #ff4081;
      border: 1px solid #ff4081;
      border-radius: 50%;
      cursor: pointer;
    }
    input[type='range']::-moz-range-thumb {
      -webkit-appearance: none;
      width: 16px;
      height: 16px;
      background: #ff4081;
      border: 1px solid #ff4081;
      border-radius: 50%;
      cursor: pointer;
    }
  }
</style>
