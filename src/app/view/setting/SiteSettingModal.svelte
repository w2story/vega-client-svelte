<script lang="ts">
  import { get } from 'svelte/store';
  import { OptionService } from '../../service/OptionService';
  import SettingModal from './SettingModal.svelte';
  import PreferenceView from './site/SiteSettingPreferenceView.svelte';

  let enableTimestamp = get(OptionService.timestamp);
  let enableBot = get(OptionService.enableBot);
  let enableCheckerBot = get(OptionService.enableCheckerBar);
  let enableDataSave = get(OptionService.enableDataSave);
  let enableCheckerRightAlign = false;

  OptionService.timestamp.subscribe((v) => (enableTimestamp = v));
  OptionService.enableBot.subscribe((v) => (enableBot = v));
  OptionService.enableCheckerBar.subscribe((v) => (enableCheckerBot = v));
  OptionService.enableDataSave.subscribe((v) => (enableDataSave = v));
  OptionService.enableCheckerRightAlign.subscribe(
    (it) => (enableCheckerRightAlign = it)
  );
</script>

<SettingModal title="사이트 설정" icon="fas fa-tools">
  <div slot="body" class="site-set-box">
    <PreferenceView
      title="채팅 시각 표기"
      enable={enableTimestamp}
      onClick={() => OptionService.setTimestamp(!enableTimestamp)}
    />
    <PreferenceView
      title="챗봇 표기"
      enable={enableBot}
      onClick={() => OptionService.setEnableBot(!enableBot)}
    />
    <PreferenceView
      title="체커바 표시"
      enable={enableCheckerBot}
      onClick={() => OptionService.setEnableCheckerBar(!enableCheckerBot)}
    />
    <PreferenceView
      title="체커바 우측 정렬"
      enable={enableCheckerRightAlign}
      onClick={() =>
        OptionService.setEnableCheckerRightAlign(!enableCheckerRightAlign)}
    />
    <PreferenceView
      title="데이터세이브"
      enable={enableDataSave}
      onClick={() => OptionService.setEnableDataSave(!enableDataSave)}
    />
  </div>
</SettingModal>

<style lang="scss">
  .site-set-box {
    width: calc(100% - 20px);
    //height: 40px;
    padding: 5px 10px;
    border-bottom: 1px solid var(--primary-hoverground-color);
  }
</style>
