<script lang="ts">
    import { getContext } from "svelte";
    export let title: string;
    export let inputValue: number;
    export let outputValue: null|number = null;
    const editing = getContext("editing");
</script>

<tr>
    <th>{title}</th>
    <td>
        <input
        id="form-field"
        type="number"
        bind:value={inputValue}
        disabled={!$editing}
        class:editable={$editing}
      />
    </td>
      
      <td>
        {#if outputValue !== null}
        <b>
          {outputValue.toFixed(0)}kg
        </b>
        {/if}
    </td>
  </tr>

  <style>
    .editable {
        color: red;
    }
    </style>