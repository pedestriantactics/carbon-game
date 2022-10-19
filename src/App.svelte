<script lang="ts">
  import { setContext } from "svelte";
  import { writable } from "svelte/store";
  import svelteLogo from "./assets/svelte.svg";
  import Counter from "./lib/Counter.svelte";
  import LineItem from "./lib/LineItem.svelte";
  import Section from "./lib/Section.svelte";
  let tableTitle = "Table Title";
  let textReadOnly = false;

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
    <button on:click={() => editing.update((e) => !e)}>
      Toggle Read Only
    </button>
    <div id="container">
      <h1>
        <b
          ><input
            id="form-field"
            type="text"
            bind:value={tableTitle}
            readonly={textReadOnly}
          /></b
        >
      </h1>
      <table>
        <thead>
          <tr id="summary">
            <th>
              <h2><b>CO2E emissions per year</b></h2>
            </th>
            <td />
            <td>
              <h2><b>{totalT.toFixed(2)}t</b></h2>
            </td>
          </tr>
        </thead>
      </table>
      <Section title="Total transportation" total={transportationTotalKg}>
        <LineItem
          title="Car miles per week"
          bind:inputValue={carMilesPerWeek}
          outputValue={carKgPerWeek}
        />
        <LineItem title="Car miles per gallon" bind:inputValue={carMPG} />
        <LineItem
          title="Bus miles per week"
          bind:inputValue={busMilesPerWeek}
          outputValue={busKgPerWeek}
        />
      </Section>
    </div>
  </div>
</main>

<style>
</style>
