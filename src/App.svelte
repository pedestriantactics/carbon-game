<script lang="ts">
  // todo
  // deal with the scrollbars in the why section
  // figure out where to put examples
  // figure out how to generate a share link
  // add all the other data

  import { setContext } from "svelte";
  import { writable } from "svelte/store";
  import LineItem from "./lib/LineItem.svelte";
  import SectionTitle from "./lib/SectionTitle.svelte";
  import "slider-color-picker";
  let primaryHue = 0;
  let secondaryHue = 10;
  export let colorPrimary = "#ff0000";
  export let colorSecondary = "#ff0000";
  let tableTitle = "Recipe title";
  let textReadOnly = false;

  function hslToRgb(h: number, s: number, l: number): [number, number, number] {
    h = h % 360;
    s = s / 100;
    l = l / 100;
    const c = (1 - Math.abs(2 * l - 1)) * s;
    const x = c * (1 - Math.abs(((h / 60) % 2) - 1));
    const m = l - c / 2;
    let r = 0,
      g = 0,
      b = 0;
    if (h < 60) {
      [r, g, b] = [c, x, 0];
    } else if (h < 120) {
      [r, g, b] = [x, c, 0];
    } else if (h < 180) {
      [r, g, b] = [0, c, x];
    } else if (h < 240) {
      [r, g, b] = [0, x, c];
    } else if (h < 300) {
      [r, g, b] = [x, 0, c];
    } else {
      [r, g, b] = [c, 0, x];
    }
    return [
      Math.round((r + m) * 255),
      Math.round((g + m) * 255),
      Math.round((b + m) * 255),
    ];
  }

  function rgbToHex(r: number, g: number, b: number) {
    return "#" + ((1 << 24) + (r << 16) + (g << 8) + b).toString(16).slice(1);
  }

  const onPrimaryColorChange = (event) => {
    let rgb = hslToRgb(primaryHue, 90, 60);
    colorPrimary = rgbToHex(rgb[0], rgb[1], rgb[2]);
  };

  const onSecondaryColorChange = (event) => {
    let rgb = hslToRgb(secondaryHue, 100, 100);
    colorSecondary = rgbToHex(rgb[0], rgb[1], rgb[2]);
  };

  const editing = writable(false);
  setContext("editing", editing);

  let why = false;

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

  let trainMilesPerWeek = 0;
  $: trainGPerMile = 177;
  $: trainKgPerWeek = (trainMilesPerWeek * trainGPerMile) / 1000;
  $: trainKgPerYear = trainKgPerWeek * 52;

  let flightHoursPerYear = 0;
  $: flightKgPerYear = flightHoursPerYear * 250;

  $: transportationTotalKgPerYear =
    carKgPerYear + busKgPerYear + flightKgPerYear;

  // food
  // first number is grams per serving
  // second number is servings per week
  // third number is kg per year
  let foodValues: Array<[string, number, number, number]> = [
    ["Vegetables", 0.02, 0, 0],
    ["Fruits", 0.01, 0, 0],
    ["Nuts", 0.08, 0, 0],
    ["Olive oil", 0.19, 0, 0],
    ["Butter", 0.7, 0, 0],
    ["Chicken", 0.04, 0, 0],
    ["Eggs", 0.1, 0, 0],
    ["Fish", 0.04, 0, 0],
    ["Beef", 0.38, 0, 0],
    ["Pork", 0.06, 0, 0],
    ["Shrimp", 16.03, 0, 0],
    ["Rice", 0.02, 0, 0],
    ["Bread", 0.04, 0, 0],
    ["Pastry", 0.12, 0, 0],
    ["Beans", 0.02, 0, 0],
    ["Coffee beans", 0.24, 0, 0],
    ["Alcohol", 0.01, 0, 0],
  ];

  $: foodKgPerYear = foodValues.reduce((total, item) => {
    item[3] = item[2] * item[1] * 52;
    return total + item[3];
  }, 0);

  let cookedMealsPerWeek = 0;
  $: cookedGPerMeal = 150;
  $: cookedMealsKgPerYear = (cookedMealsPerWeek * cookedGPerMeal * 52) / 1000;

  // total
  $: totalT =
    (transportationTotalKgPerYear + foodKgPerYear + cookedMealsKgPerYear) /
    1000;
</script>

<main
  style="--color-primary: {colorPrimary}; --color-secondary: {colorSecondary};"
  use:onPrimaryColorChange
  use:onSecondaryColorChange
>
  <div id="site-container">
    <div id="top">
      {#if !why}
        <div class="right">
          <button on:click={() => editing.update((e) => !e)}>
            {#if $editing}Done editing{:else}Edit recipe{/if}
          </button>
        </div>
      {/if}
    </div>

    <div id="bottom">
      <div class="left">
        <button
          on:click={() => {
            why = !why;
            editing.update(() => false);
          }}
        >
          {#if why}Okay cool{:else}Why?{/if}
        </button>
      </div>
    </div>

    {#if !why}
      <div id="content-container">
        <div id="table-container">
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
            title="Total yearly C02e emissions"
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
            title="Transportation"
            total={transportationTotalKgPerYear}
          />
          <LineItem
            title="Car miles per week"
            bind:inputValue={carMilesPerWeek}
            outputValue={carKgPerYear}
            indentLevel={1}
            note="8.8kg CO2e per gallon of gas"
            reference="https://www.epa.gov/ghgemissions/inventory-us-greenhouse-gas-emissions-and-sinks-1990-2018?"
          />
          <LineItem
            title="Car miles per gallon"
            bind:inputValue={carMPG}
            indentLevel={1}
          />
          <LineItem
            title="Bus miles per week"
            bind:inputValue={busMilesPerWeek}
            outputValue={busKgPerYear}
            indentLevel={1}
            note="0.2kg CO2e per mile"
            reference="https://www.carbonindependent.org/20.html"
          />
          <LineItem
            title="Train miles per week"
            bind:inputValue={trainMilesPerWeek}
            outputValue={trainKgPerYear}
            indentLevel={1}
            note="0.17kg CO2e per mile"
            reference="https://www.carbonindependent.org/20.html"
          />
          <LineItem
            title="Flight hours per year"
            bind:inputValue={flightHoursPerYear}
            outputValue={flightKgPerYear}
            indentLevel={1}
            note="250kg CO2e per hour"
          />

          <SectionTitle
            title="Food (as servings per week)"
            total={foodKgPerYear}
          />

          {#each foodValues as item}
            <LineItem
              title={item[0]}
              bind:inputValue={item[2]}
              outputValue={item[3]}
              indentLevel={1}
              note="{item[1]}g CO2e per serving"
            />
          {/each}

          <LineItem
            title="Amount of hot meals per week"
            bind:inputValue={cookedMealsPerWeek}
            outputValue={cookedMealsKgPerYear}
            indentLevel={0}
            note="{cookedGPerMeal}g emitted to cook a single meal"
          />

          <div id="table-footer">
            <p>A project by <a href="http://imdantaylor.com">Dan Taylor</a></p>
          </div>
        </div>
        {#if $editing}
          <div id="footer-container">
            <div class="color-container">
              <div class="color-title">
                <h3>Recipe color</h3>
              </div>
              <input
                class="color-slider"
                type="range"
                min="0"
                max="360"
                bind:value={primaryHue}
                on:input={onPrimaryColorChange}
              />
            </div>
            <!-- <div class="color-container">
            <div class="color-title">
              <h3>Background color</h3>
            </div>
            <input
              class="color-slider"
              type="range"
              min="0"
              max="360"
              bind:value={secondaryHue}
              on:input={onSecondaryColorChange}
            />
          </div> -->
          </div>
        {/if}
      </div>
    {/if}

    {#if why}
      <div id="why-container">
        <div id="why-container-inner">
          <p>
            CO2 emissions are kind of like calories. They don’t represent
            overall health, but if you’re struggling with your weight, it’s a
            good place to start. On earth, CO2 emissions contribute to a buildup
            of greenhouse gases. Greenhouse gases trap heat, preventing it from
            dissipating into the atmosphere.
          </p>
          <p>
            We have been cranking out a staggeringly massive amount of CO2 in
            the last hundred years. All living things on this planet have
            handled gradual changes very well but quick changes to the
            environment are difficult to deal with. Curbing CO2 emissions to the
            EPA’s recommended 2.5 tons a year is one of the most effective
            things we can do to ensure our future.
          </p>
          <p>
            These recipes use C02e which means CO2 equivalent. Not everything
            generates CO2 itself, like running a lightbulb, but the approximate
            CO2 emitted to generate the power to run the lightbulb is what’s
            being factored.
          </p>
        </div>
      </div>
    {/if}
  </div>
</main>

<style>
  :root {
    color: var(--color-primary);
    --width: 640px;
  }
  .thiccc {
    height: 8px;
    margin-top: 1rem;
  }

  p {
    margin-bottom: 0.5rem;
  }

  #top {
    position: fixed;
    top: 0.5em;
    right: 0.5em;
    left: 0.5em;
  }
  #bottom {
    position: fixed;
    bottom: 0.5em;
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
    background-color: var(--color-secondary);
  }

  #table-container {
    margin-bottom: 2em;
    border: 2px solid var(--color-primary);
    padding: 0.5em;
  }

  #content-container {
    margin-top: 4rem;
    margin-left: auto;
    margin-right: auto;
    padding: 0.5rem;
    max-width: var(--width);
  }

  #why-container {
    width: 100vw;
    height: 100vh;
    display: grid;
    place-items: center;
  }

  #why-container-inner {
    max-width: var(--width);
  }

  #why-container-inner p {
    text-align: center;
    font-size: var(--h2-font-size);
    margin-bottom: 1em;
  }

  #summary {
    border-bottom: 8px solid var(--color-primary);
  }

  .color-container {
    margin-bottom: 1rem;
    display: grid;
    grid-template-columns: auto 1fr;
    align-items: center;
  }

  .color-title {
    float: left;
    margin-right: 1em;
  }

  .color-slider {
    float: right;
    -webkit-appearance: none;
    width: 100%;
    margin: 0;
    padding: 0;
    height: 8px;
    border: none;
    background: linear-gradient(
      to right,
      #ff0000 0%,
      #ffff00 17%,
      #00ff00 33%,
      #00ffff 50%,
      #0000ff 67%,
      #ff00ff 83%,
      #ff0000 100%
    );
    outline: none;
  }

  .color-slider::-webkit-slider-thumb {
    -webkit-appearance: none;
    appearance: none;
    width: 16px;
    height: 16px;
    background: var(--color-primary);
    cursor: pointer;
  }

  .color-slider::-moz-range-thumb,
  .color-slider::-webkit-slider-thumb {
    width: 16px;
    height: 16px;
    background: var(--color-primary);
    cursor: pointer;
  }

  @media screen and (max-width: 520px) {
  }
</style>
