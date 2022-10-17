<script lang="ts">
  import svelteLogo from "./assets/svelte.svg";
  import Counter from "./lib/Counter.svelte";
  let text = "";
</script>

<main>
  <div id="site-container">
    <div id="container">
      <h1><b>Table title</b></h1>
      <table>
        <thead>
          <tr id="summary">
            <th>
              <h2><b>CO2E emissions per year</b></h2>
            </th>
            <td />
            <td>
              <h2><b>2.8t</b></h2>
            </td>
          </tr>
        </thead>
      </table>
      <table>
        <thead>
          <tr><th><b>Total Transportation</b></th><td /><td><b>32kg</b></td></tr
          >
        </thead>
        <tbody>
          <tr>
            <th>Gasoline car miles per week</th>
            <td>26</td><td><b>32.9kg</b></td>
          </tr>
          <tr>
            <th>Bus miles per week</th>
            <td>26</td><td><b>32.9kg</b></td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</main>

<style>
  :root {
    font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
    font-size: 18px;
    line-height: 1.5em;
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

  h1 {
    font-size: 2em;
    margin-top: 0.5em;
    margin-bottom: 0.5em;
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
