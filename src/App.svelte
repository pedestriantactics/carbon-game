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
  let tableTitle = "Table Title";
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

  $: transportationTotalKg = carKgPerYear + busKgPerYear;

  $: totalT = transportationTotalKg / 1000;
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
            title="Total yearly C02E emissions"
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
            <p>A project by <a href="http://imdantaylor.com">Dan Taylor</a></p>
          </div>
        </div>
        {#if $editing}
          <div id="footer-container">
            <div class="color-container">
              <div class="color-title">
                <h3>Table color</h3>
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
            The impact of most of our actions is hidden to us. Money has created
            the illusion that we have earned the right to do things that we can
            afford without seeing the big picture. If we base our decisions off
            their ecological consequences we learn more and strengthen our
            connection with this beautiful world we've been born into.
          </p>
          <p>
            Whether you believe climate change was caused by us or not, we have
            been churning out more C02 than any previous generation. The planet
            can deal with this but humans can't breathe the stuff. The EPA has
            set a strong recommendation that we reduce our yearly emissions to
            2.5 tons per person. This is a tool to help you figure out how you
            can do this for yourself.
          </p>
          <p>Share your strategy with your friends.</p>
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
    padding: 0.5rem;
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
