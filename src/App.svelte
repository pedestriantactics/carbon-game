<script lang="ts">
  import { setContext } from "svelte";
  import { writable } from "svelte/store";
  import LineItem from "./lib/LineItem.svelte";
  import SectionTitle from "./lib/SectionTitle.svelte";
  let tableTitle = "Table Title";
  let textReadOnly = false;

  // max tons
  let maxT = 2.5;

  // transportation
  let carMilesPerWeek = 10;
  let carMPG = 30;
  $: carGPerGallon = 8887;
  $: carKgPerWeek = ((carMilesPerWeek / carMPG) * carGPerGallon) / 1000;
  $: carKgPerYear = carKgPerWeek * 52;

  let busMilesPerWeek = 40;
  $: busGPerMile = 200;
  $: busKgPerWeek = (busMilesPerWeek * busGPerMile) / 1000;
  $: busKgPerYear = busKgPerWeek * 52;

  $: transportationTotalKg = carKgPerYear + busKgPerYear;

  $: totalT = transportationTotalKg / 1000;

  const editing = writable(false);
  setContext("editing", editing);
</script>

<main>
  <div id="site-container">
    <div id="top">
      <div class="right">
        <button on:click={() => editing.update((e) => !e)}>
          {#if $editing}Done editing{:else}Edit recipe{/if}
        </button>
      </div>
    </div>
    <div id="container">
      <input
        class="title"
        type="text"
        bind:value={tableTitle}
        disabled={!$editing}
        class:editable={$editing}
        readonly={textReadOnly}
      />
      <hr />
      <SectionTitle
        title="Total C02E emissions"
        total={totalT}
        suffix="t"
        roundDigits={2}
        useRule={false}
        useH2={true}
      />
      <p>
        {#if totalT.toFixed(2) == maxT.toFixed(2)}
          Includes no emissions that must be reduced or offset
        {:else}
          {#if totalT < maxT}
            {(maxT - totalT).toFixed(2)}t less than the emissions threshold
          {/if}
          {#if totalT > maxT}
            Includes {(totalT - maxT).toFixed(2)}t of emissions that must be
            reduced or offset
          {/if}
        {/if}
      </p>

      <hr class="thiccc" />

      <SectionTitle
        title="Ground transportation"
        total={transportationTotalKg}
      />
      <LineItem
        title="Car miles per week"
        bind:inputValue={carMilesPerWeek}
        outputValue={carKgPerWeek}
        indentLevel={1}
      />
      <LineItem
        title="Car miles per gallon"
        bind:inputValue={carMPG}
        indentLevel={1}
      />
      <LineItem
        title="Bus miles per week"
        bind:inputValue={busMilesPerWeek}
        outputValue={busKgPerWeek}
        indentLevel={1}
      />
      <div id="table-footer">
        <p>Create your own at imdantaylor.com</p>
      </div>
    </div>
  </div>
</main>

<style>
  .thiccc {
    height: 8px;
    margin-top: 1rem;
  }

  p {
    margin-bottom: 0.5rem;
  }
  #top {
    position: absolute;
    top: 0.5em;
    right: 0.5em;
    left: 0.5em;
  }
  .right {
    float: right;
  }
  .title {
    width: 70%;
    font-size: var(--h1-font-size);
    font-weight: bold;
    margin-bottom: 0.5rem;
  }
  #table-footer {
    margin-top: 1rem;
  }

  #site-container {
    width: 100vw;
    height: 100vh;
    display: grid;
    place-items: center;
  }

  #container {
    border: 2px solid var(--color-primary);
    width: 480px;
    padding: 0.5em;
  }

  #summary {
    border-bottom: 8px solid var(--color-primary);
  }
</style>
