<script>
  import { getContext, setContext } from "svelte"
  import { writable } from "svelte/store"
  import { CoreRichTextField, CoreTextArea } from "@budibase/bbui"
  import Field from "./Field.svelte"
  import Button from "../Button.svelte"
  import { processStringSync } from "@budibase/string-templates"

  export let field
  export let label
  export let placeholder
  export let disabled = false
  export let readonly = false
  export let validation
  export let defaultValue = ""
  export let format = "auto"
  export let onChange
  export let helpText = null

  export let uploadDatasourceId: string | null = null
  export let uploadBucket: string | null = null
  export let uploadKey: string | null = null

  let fieldState
  let fieldApi
  let fieldSchema
  let fileInput: HTMLInputElement | null = null

  const context = getContext("context")
  const component = getContext("component")
  const layout = getContext("layout")
  const newContext = writable($component)
  setContext("component", newContext)
  const { API, notificationStore } = getContext("sdk")

  // Determine whether we should use a rich or plain text component.
  // We treat undefined as "auto".
  $: useRichText =
    format === "rich" || (format !== "plain" && fieldSchema?.useRichText)

  // Extract the settings height so we can pass it on to the rich text field.
  // We then wipe the height style so that the field will automatically size
  // itself based on the height of the rich text field.
  let height
  $: {
    height = $component.styles?.normal?.height || "150px"
    newContext.set({
      ...$component,
      styles: {
        ...$component.styles,
        normal: {
          ...$component.styles.normal,
          height: undefined,
        },
      },
    })
  }

  const handleChange = e => {
    const changed = fieldApi.setValue(e.detail)
    if (onChange && changed) {
      onChange({ value: e.detail })
    }
  }

  const openFileDialog = () => {
    fileInput?.click()
  }

  const insertMarkdown = (url: string, filename: string) => {
    const md = `![${filename}](${url})\n`
    const current = fieldApi.getValue() || ""
    fieldApi.setValue(`${current}\n${md}`)
  }

  const handleFileSelected = async (e: Event) => {
    const file = (e.target as HTMLInputElement)?.files?.[0]
    if (!file || !uploadDatasourceId || !uploadBucket || !uploadKey) {
      return
    }
    try {
      const processedKey = processStringSync(uploadKey, $context)
      const { publicUrl } = await API.externalUpload(
        uploadDatasourceId,
        uploadBucket,
        processedKey,
        file
      )
      if (publicUrl) {
        insertMarkdown(publicUrl, file.name)
      }
    } catch (err) {
      notificationStore.actions.error(`Error uploading file: ${err}`)
    } finally {
      if (fileInput) {
        fileInput.value = ""
      }
    }
  }
</script>

<Field
  {label}
  {field}
  {disabled}
  {readonly}
  {validation}
  {defaultValue}
  {helpText}
  type="longform"
  bind:fieldState
  bind:fieldApi
  bind:fieldSchema
>
  {#if fieldState}
    {#if !fieldState.readonly && !fieldState.disabled && uploadDatasourceId && uploadBucket && uploadKey}
      <div class="upload-button">
        <Button type="secondary" on:click={openFileDialog}>Upload Image</Button>
        <input
          type="file"
          accept="image/*"
          bind:this={fileInput}
          on:change={handleFileSelected}
          style="display: none"
        />
      </div>
    {/if}
    {#if useRichText}
      <CoreRichTextField
        value={fieldState.value}
        on:change={handleChange}
        disabled={fieldState.disabled}
        readonly={fieldState.readonly}
        error={fieldState.error}
        id={fieldState.fieldId}
        {placeholder}
        {height}
        fullScreenOffset={{
          x: $layout.screenXOffset,
          y: $layout.screenYOffset,
        }}
        easyMDEOptions={{
          hideIcons: $context.device.mobile ? ["side-by-side", "guide"] : [],
        }}
      />
    {:else}
      <CoreTextArea
        value={fieldState.value}
        on:change={handleChange}
        disabled={fieldState.disabled}
        readonly={fieldState.readonly}
        error={fieldState.error}
        id={fieldState.fieldId}
        {placeholder}
        minHeight={height}
      />
    {/if}
  {/if}
</Field>

<style>
  .upload-button {
    margin-bottom: var(--spacing-s);
  }
</style>
