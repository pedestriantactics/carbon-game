<script lang="ts">
  import svelteLogo from "./assets/svelte.svg";
  import Counter from "./lib/Counter.svelte";
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
</script>

<main>
  <div id="site-container">
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
      <table>
        <thead>
          <tr
            ><th><b>Total Transportation</b></th><td /><td
              ><b>{transportationTotalKg.toFixed(0)}kg</b></td
            ></tr
          >
        </thead>
        <tbody>
          <tr>
            <th>Gasoline car miles per week</th>
            <td
              ><input
                id="form-field"
                type="number"
                bind:value={carMilesPerWeek}
                readonly={textReadOnly}
              /></td
            ><td><b>{carKgPerYear.toFixed(0)}kg</b></td>
          </tr>
          <tr>
            <th>Car miles per gallon</th>
            <td
              ><input
                id="form-field"
                type="number"
                bind:value={carMPG}
                readonly={textReadOnly}
              /></td
            ><td />
          </tr>
          <tr>
            <th>Bus miles per week</th>
            <td
              ><input
                id="form-field"
                type="number"
                bind:value={busMilesPerWeek}
                readonly={textReadOnly}
              /></td
            ><td><b>{busKgPerYear.toFixed(0)}kg</b></td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</main>

<style>
  :root,
  input {
    font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
    font-size: 18px;
    line-height: 1.5em;
  }

  /* INPUTS */

  input {
    border: none;
    background-color: transparent;
    padding: 0;
    margin: 0;
  }
  input[type="text"],
  h1 {
    font-size: 1.5em;
    font-weight: bold;
    margin-top: 0.2em;
    margin-bottom: 0.2em;
  }
  input[type="number"] {
    max-width: 3em;
    margin-left: 1em;
    margin-right: 1em;
    text-align: right;
    -moz-appearance: textfield;
  }

  textarea:focus,
  input:focus {
    outline: none;
  }

  /* Chrome, Safari, Edge, Opera */
  input::-webkit-outer-spin-button,
  input::-webkit-inner-spin-button {
    -webkit-appearance: none;
    appearance: none;
    margin: 0;
  }

  /* Firefox */
  input[type="number"] {
    -moz-appearance: textfield;
  }

  #site-container {
    width: 100vw;
    height: 100vh;
    display: grid;
    place-items: center;
  }
  #container {
    border: 2px solid black;
    min-width: 480px;
    min-width: 480px;
    padding: 0.5em;
  }

  h2 {
    font-size: 1.4em;
    margin-top: 0.5em;
    margin-bottom: 0.5em;
  }

  b {
    font-weight: bold;
  }

  table {
    border-collapse: collapse;
    width: 100%;
  }

  #summary {
    border-bottom: 8px solid black;
  }

  thead td,
  thead th {
    padding-left: 0px;
  }

  td,
  th {
    padding-top: 0.5em;
    padding-bottom: 0.5em;
    border-top: 2px solid black;
  }
  th {
    padding-left: 1em;
    text-align: left;
  }
  td {
    text-align: right;
  }
</style>
