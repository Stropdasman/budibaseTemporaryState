<script lang="ts">
  import "@spectrum-css/statuslight"

  export let size: string = "M"
  export let celery: boolean = false
  export let yellow: boolean = false
  export let fuchsia: boolean = false
  export let indigo: boolean = false
  export let seafoam: boolean = false
  export let chartreuse: boolean = false
  export let magenta: boolean = false
  export let purple: boolean = false
  export let neutral: boolean = false
  export let info: boolean = false
  export let positive: boolean = false
  export let notice: boolean = false
  export let negative: boolean = false
  export let disabled: boolean = false
  export let active: boolean = false
  export let color: string | undefined = undefined
  export let square: boolean = false
  export let hoverable: boolean = false
</script>

<!-- svelte-ignore a11y-no-static-element-interactions -->
<!-- svelte-ignore a11y-click-events-have-key-events -->
<div
  on:click
  class="spectrum-StatusLight spectrum-StatusLight--size{size}"
  class:custom={!!color}
  class:square
  class:hoverable
  style={`--color: ${color ?? "var(--spectrum-global-color-gray-400)"};`}
  class:spectrum-StatusLight--celery={celery}
  class:spectrum-StatusLight--yellow={yellow}
  class:spectrum-StatusLight--fuchsia={fuchsia}
  class:spectrum-StatusLight--indigo={indigo}
  class:spectrum-StatusLight--seafoam={seafoam}
  class:spectrum-StatusLight--chartreuse={chartreuse}
  class:spectrum-StatusLight--magenta={magenta}
  class:spectrum-StatusLight--purple={purple}
  class:spectrum-StatusLight--neutral={neutral}
  class:spectrum-StatusLight--info={info}
  class:spectrum-StatusLight--positive={positive}
  class:spectrum-StatusLight--notice={notice}
  class:spectrum-StatusLight--negative={negative}
  class:spectrum-StatusLight--disabled={disabled}
  class:spectrum-StatusLight--active={active}
  class:withText={!!$$slots.default}
>
  <slot />
</div>

<style>
  .spectrum-StatusLight {
    display: flex;
    flex-direction: row;
    justify-content: center;
    align-items: center;
    --spectrum-statuslight-info-text-gap: 4px;
    min-height: 0;
    padding-top: 0;
    padding-bottom: 0;
  }
  .spectrum-StatusLight.withText::before {
    margin-right: 10px;
  }

  .spectrum-StatusLight::before {
    transition: background-color ease-out 160ms;
  }

  .custom::before {
    background-color: var(--color) !important;
  }
  .square::before {
    width: 14px;
    height: 14px;
    border-radius: 4px;
    margin: 0;
  }
  .hoverable:hover {
    cursor: pointer;
    color: var(--spectrum-global-color-gray-900);
  }

  .spectrum-StatusLight--sizeXS::before {
    width: 10px;
    height: 10px;
    border-radius: 2px;
  }

  .spectrum-StatusLight--disabled::before {
    background-color: var(--spectrum-global-color-gray-400) !important;
  }
</style>
