<script lang="ts">
  import { onMount, onDestroy } from 'svelte'

  export let recipients: string[]

  let recipientsColumn: HTMLElement
  let visibleCount = 1
  let hiddenCount = 0

  const ellipsisWidth = 16

  onMount(() => {
    handleResize()
    window.addEventListener('resize', handleResize)
  })

  onDestroy(() => {
    window.removeEventListener('resize', handleResize)
  })

  function handleResize() {
    if (recipientsColumn) {
      const recipientsListItems: NodeListOf<HTMLElement> = recipientsColumn.querySelectorAll(
        '.mail-item'
      )
      let recipientsColumnWidth = recipientsColumn.offsetWidth

      visibleCount = 1
      hiddenCount = 0

      if (recipientsListItems.length > 1) {
        recipientsListItems.forEach((recipientListItem, index) => {
          const recipientItemWidth = recipientListItem.offsetWidth

          if (
            recipientItemWidth !== recipientsColumnWidth &&
            recipientItemWidth + ellipsisWidth > recipientsColumnWidth &&
            index !== 0
          ) {
            hiddenCount++
          } else {
            index !== 0 && visibleCount++
          }
          recipientsColumnWidth -= recipientItemWidth
        })
      }
    }
  }
</script>

<style lang="scss">
  .wrapper {
    align-items: center;
    display: flex;
    justify-content: space-between;
    height: 100%;
    white-space: nowrap;
  }
  .item-wrapper {
    margin-right: 0.125rem;
    overflow-x: hidden;
    text-overflow: ellipsis;
    width: 100%;
    white-space: nowrap;
  }
  .mail-item {
    display: inline-block;
  }
  .mail-item-initial {
    display: initial;
  }
  .badge {
    align-items: center;
    background-color: $color-primary;
    border-radius: 3px;
    color: $color-secondary;
    display: flex;
    padding: 0 0.5rem;
    height: 100%;
  }
</style>

<!--
@component
Receives a `recipients` prop and renders all recepients

- Usage:
  ```tsx
  <RecipientsDisplay {recipients} />
  ```
-->
<div class="wrapper">
  <div class="item-wrapper" bind:this={recipientsColumn}>
    {#each recipients as recipient, index}
      <span
        class={`mail-item ${visibleCount === 1 && index === 0 ? 'mail-item-initial' : ''}`}>
        {recipient}{#if recipients.length - 1 !== index},&nbsp;{/if}
      </span>
    {/each}
  </div>
  {#if hiddenCount > 0}
    <div class="badge">+ {hiddenCount}</div>
  {/if}
</div>
