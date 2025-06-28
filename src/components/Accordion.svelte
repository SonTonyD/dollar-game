<script>
  export let title = "";
  let open = false;
</script>

<style>
  .accordion {
    border: 1px solid #ccc;
    border-radius: 8px;
    margin: 10px 0;
  }
  .header {
    background-color: #f3f3f3;
    padding: 10px;
    cursor: pointer;
    font-weight: bold;
  }
  .content {
    padding: 10px;
  }
</style>

<div class="accordion">
  <!-- svelte-ignore a11y_no_static_element_interactions -->
  <!-- svelte-ignore a11y_click_events_have_key_events -->
  <div class="header" on:click={() => open = !open}>
    {open ? "▼" : "▶"} {title}
  </div>
  {#if open}
    <div class="content">
      <slot />
    </div>
  {/if}
</div>