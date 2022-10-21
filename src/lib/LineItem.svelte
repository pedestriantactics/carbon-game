<script lang="ts">
  import { getContext } from "svelte";
  export let title: string;
  export let inputValue: number;
  export let outputValue: null | number = null;
  export let indentLevel: number = 0;
  const editing = getContext("editing");
</script>

<div id="container" style="margin-left: calc(1em * {indentLevel});">
  <div class="left">
    <h3>{title}</h3>
  </div>
  <div class="right">
    <div class="right-content">
      <input
        type="number"
        bind:value={inputValue}
        disabled={!$editing}
        class:editable={$editing}
      />
    </div>
    <div class="right-content" id="output">
      {#if outputValue !== null}
        <h3>{outputValue.toFixed(0)}kg</h3>
      {/if}
    </div>
  </div>
</div>

<style>
  #container {
    height: 1.8em;
    padding-top: 0.5em;
    padding-bottom: 0.5em;
    display: flex;
    justify-content: space-between;
    align-items: center;
    border-bottom: 2px solid var(--color-primary);
  }

  input {
    color: var(--color-primary);
    font-size: var(--h3-font-size);
    text-align: right;
    float: right;
    width: 100%;
  }

  .left {
    float: left;
  }

  .right {
    float: right;
    display: flex;
    align-items: center;
  }

  .right-content {
    text-align: right;
    width: 4em;
  }

  #output {
    font-weight: bold;
  }
</style>
