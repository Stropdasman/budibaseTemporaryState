<script>
  import DevicePreviewSelect from "./DevicePreviewSelect.svelte"
  import AppPreview from "./AppPreview.svelte"
  import { screenStore, appStore, selectedScreen } from "@/stores/builder"
  import UndoRedoControl from "@/components/common/UndoRedoControl.svelte"
  import ScreenErrorsButton from "./ScreenErrorsButton.svelte"
  import { Divider } from "@budibase/bbui"
  import { ScreenVariant } from "@budibase/types"

  $: isPDF = $selectedScreen?.variant === ScreenVariant.PDF
</script>

<div class="app-panel">
  <div class="drawer-container" />
  <div class="header">
    <div class="header-left">
      <UndoRedoControl store={screenStore.history} />
    </div>
    <div class="header-right">
      {#if !isPDF}
        {#if $appStore.clientFeatures.devicePreview}
          <DevicePreviewSelect />
        {/if}
        <Divider vertical />
      {/if}
      <ScreenErrorsButton />
    </div>
  </div>
  <div class="content">
    {#key $appStore.version}
      <AppPreview />
    {/key}
  </div>
</div>

<style>
  .app-panel {
    min-width: 410px;
    flex: 1 1 auto;
    overflow-y: auto;
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
    align-items: stretch;
    padding: 9px 10px 12px 10px;
    position: relative;
    transition: width 360ms ease-out;
  }
  .drawer-container {
    position: absolute;
    height: 100%;
    width: 100%;
    overflow: hidden;
    top: 0;
    left: 0;
  }
  .header {
    display: flex;
    margin-bottom: 9px;
  }

  .header-left {
    display: flex;
  }
  .header-left :global(div) {
    border-right: none;
  }
  .header-right {
    margin-left: auto;
    display: flex;
    flex-direction: row;
    justify-content: flex-start;
    align-items: center;
    gap: var(--spacing-l);
  }
  .content {
    flex: 1 1 auto;
  }
</style>
