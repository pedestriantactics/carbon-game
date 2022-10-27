<script lang="ts">
  import { onMount, setContext } from "svelte";
  import { writable } from "svelte/store";
  import LineItem from "./lib/LineItem.svelte";
  import SectionTitle from "./lib/SectionTitle.svelte";
  import "slider-color-picker";

  // let storedValues = new Map<string, any>();

  // saving and loading

  let url = new URL(window.location.href);

  function loadValue(key: string, defaultValue: string): string {
    // add it to the stored variables if it's a type that should be loaded
    return (
      url.searchParams.get(key) ?? localStorage.getItem(key) ?? defaultValue
    );
  }

  function loadNumber(key: string, defaultValue: number): number {
    return parseFloat(loadValue(key, defaultValue.toString()));
  }

  function storeStringOrNumber(key: string, value: any) {
    if (typeof value === "string") {
      storeValue(key, value);
    } else if (typeof value === "number") {
      storeNumber(key, value);
    }
  }

  function storeValue(key: string, value: string) {
    url.searchParams.set(key, value);
    localStorage.setItem(key, value);
  }

  function storeNumber(key: string, value: number) {
    if (!value) value = 0;
    storeValue(key, value.toString());
  }

  let primaryHue = loadNumber("primaryHue", 220);
  // storedValues["primaryHue"] = primaryHue;
  // storedValues.set("primaryHue", primaryHue);

  let secondaryHue = loadNumber("secondaryHue", 200);
  // storedValues.set("secondaryHue", secondaryHue);

  export let colorPrimary = "#ff0000";
  export let colorSecondary = "#ff0000";

  let tableTitle = loadValue("tableTitle", "Example title");
  // storedValues["tableTitle"] = tableTitle;
  // storedValues.set("tableTitle", tableTitle);

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

  const onPrimaryColorChange = () => {
    let rgb = hslToRgb(primaryHue, 100, 60);
    colorPrimary = rgbToHex(rgb[0], rgb[1], rgb[2]);
  };

  const onSecondaryColorChange = () => {
    let rgb = hslToRgb(secondaryHue, 100, 80);
    colorSecondary = rgbToHex(rgb[0], rgb[1], rgb[2]);
  };

  const editing = writable(false);
  setContext("editing", editing);

  let why = false;

  // max tons
  let maxT = 2.5;

  // transportation
  let carMilesPerWeek = loadNumber("carMilesPerWeek", 0);
  let carMPG = loadNumber("carMPG", 20);
  $: carGPerGallon = 8887;
  $: carKgPerWeek = ((carMilesPerWeek / carMPG) * carGPerGallon) / 1000;
  $: carKgPerYear = carKgPerWeek * 52;

  $: KgPerKWhElectric = 0.39;
  let evMilesPerWeek = loadNumber("evMilesPerWeek", 0);
  $: evKgPerWeek = evMilesPerWeek * 0.25 * KgPerKWhElectric;
  $: evKgPerYear = evKgPerWeek * 52;

  let busMilesPerWeek = loadNumber("busMilesPerWeek", 0);
  $: busGPerMile = 200;
  $: busKgPerWeek = (busMilesPerWeek * busGPerMile) / 1000;
  $: busKgPerYear = busKgPerWeek * 52;

  let trainMilesPerWeek = loadNumber("trainMilesPerWeek", 0);
  $: trainGPerMile = 177;
  $: trainKgPerWeek = (trainMilesPerWeek * trainGPerMile) / 1000;
  $: trainKgPerYear = trainKgPerWeek * 52;

  let flightHoursPerYear = loadNumber("flightHoursPerYear", 0);
  $: flightKgPerYear = flightHoursPerYear * 250;

  $: transportationTotalKgPerYear =
    carKgPerYear + evKgPerYear + busKgPerYear + flightKgPerYear;

  // food
  // first number is grams per serving
  // second number is servings per week
  // third number is kg per year
  let foodValues: Array<[string, number, number, number]> = [
    ["Vegetables", 0.11, loadNumber("Vegetables", 0), 0],
    ["Fruits", 0.23, loadNumber("Fruits", 0), 0],
    ["Nuts", 0.07, loadNumber("Nuts", 0), 0],
    ["Olive oil", 0.03, loadNumber("Olive oil", 0), 0],
    ["Butter", 0.14, loadNumber("Butter", 0), 0],
    ["Chicken", 0.53, loadNumber("Chicken", 0), 0],
    ["Eggs", 0.2, loadNumber("Eggs", 0), 0],
    ["Fish", 0.45, loadNumber("Fish", 0), 0],
    ["Beef", 4.9, loadNumber("Fish", 0), 0],
    ["Pork", 0.75, loadNumber("Pork", 0), 0],
    ["Shrimp", 160.3, loadNumber("Shrimp", 0), 0],
    ["Rice", 0.09, loadNumber("Rice", 0), 0],
    ["Bread", 0.03, loadNumber("Bread", 0), 0],
    ["Pastry", 0.38, loadNumber("Pastry", 0), 0],
    ["Beans", 0.07, loadNumber("Beans", 0), 0],
    ["Coffee beans", 0.09, loadNumber("Coffee beans", 0), 0],
    ["Alcohol", 0.19, loadNumber("Alcohol", 0), 0],
    ["Cheese", 0.43, loadNumber("Cheese", 0), 0],
    ["Milk", 0.37, loadNumber("Milk", 0), 0],
  ];

  $: foodKgPerYear = foodValues.reduce((total, item) => {
    item[3] = item[2] * item[1] * 52;
    return total + item[3];
  }, 0);

  let cookedMealsPerWeek = loadNumber("cookedMealsPerWeek", 0);
  $: cookedGPerMeal = 150;
  $: cookedMealsKgPerYear = (cookedMealsPerWeek * cookedGPerMeal * 52) / 1000;

  // home
  let homeSquareFeet = loadNumber("homeSquareFeet", 0);
  let homeDegreesAdjustedDownInWinter = loadNumber(
    "homeDegreesAdjustedDownInWinter",
    0
  );
  $: averageHouseKgForHeatingAndCooling = 2268;
  $: averageSquareFootageOfHome = 2301;
  $: totalKgFromHeating =
    (homeSquareFeet / averageSquareFootageOfHome) *
    averageHouseKgForHeatingAndCooling;
  $: totalKgReduced =
    totalKgFromHeating * 0.03 * homeDegreesAdjustedDownInWinter;
  $: homeKgPerYear = totalKgFromHeating - totalKgReduced;

  // electricity

  let lightbulbQuantity = loadNumber("lightbulbQuantity", 0);
  let lightBulbHoursPerDay = loadNumber("lightBulbHoursPerDay", 0);
  $: lightBulbKgPerYear =
    lightBulbHoursPerDay * 0.06 * lightbulbQuantity * KgPerKWhElectric * 365;

  let computerHoursPerDay = loadNumber("computerHoursPerDay", 0);
  $: computerKgPerYear = computerHoursPerDay * 0.047 * KgPerKWhElectric * 365;

  let dishwasherUsesPerMonth = loadNumber("dishwasherUsesPerMonth", 0);
  $: dishwasherKgPerYear =
    dishwasherUsesPerMonth * 1.44 * KgPerKWhElectric * 12;

  let clothesWasherUsesPerMonth = loadNumber("clothesWasherUsesPerMonth", 0);
  $: clothesWasherKgPerYear =
    clothesWasherUsesPerMonth * 0.8 * KgPerKWhElectric * 12;

  let clothesDryerUsesPerMonth = loadNumber("clothesDryerUsesPerMonth", 0);
  $: clothesDrykerKgPerYear =
    clothesDryerUsesPerMonth * 2.5 * KgPerKWhElectric * 12;

  let fridgeQuantity = loadNumber("fridgeQuantity", 0);
  $: fridgeKgPerYear = fridgeQuantity * 408 * KgPerKWhElectric;

  $: totalElectricityKgPerYear =
    lightBulbKgPerYear +
    computerKgPerYear +
    dishwasherKgPerYear +
    clothesWasherKgPerYear +
    clothesDrykerKgPerYear +
    fridgeKgPerYear;

  // water
  let gallonsOfHotWaterUsedPerDay = loadNumber(
    "gallonsOfHotWaterUsedPerDay",
    0
  );
  $: hotWaterKgPerYear = gallonsOfHotWaterUsedPerDay * 0.39 * 0.26 * 365;

  // purchases
  // first number is kg per item
  // second number quanity
  // third number is kg per year
  let purchaseValues: Array<[string, number, number, number]> = [
    ["iPhone", 78, loadNumber("iPhone", 0), 0],
    ["Laptop or iPad", 422.5, loadNumber("Laptop", 0), 0],
    ["Furniture item", 47, loadNumber("Furniture", 0), 0],
    ["Clothing item", 33.4, loadNumber("Clothing item", 0), 0],
    ["Car", 8000, loadNumber("Car", 0), 0],
  ];

  $: purchaseKgPerYear = purchaseValues.reduce((total, item) => {
    item[3] = item[2] * item[1];
    return total + item[3];
  }, 0);

  // total
  $: totalKgPerYear =
    transportationTotalKgPerYear +
    foodKgPerYear +
    cookedMealsKgPerYear +
    homeKgPerYear +
    totalElectricityKgPerYear +
    hotWaterKgPerYear +
    purchaseKgPerYear;

  $: totalT = totalKgPerYear / 1000;

  // updating url

  $: {
    // let url = new URL(window.location.href);
    storeValue("tableTitle", tableTitle);
    storeNumber("primaryHue", primaryHue);
    storeNumber("secondaryHue", secondaryHue);
    storeNumber("carMilesPerWeek", carMilesPerWeek);
    storeNumber("carMPG", carMPG);
    storeNumber("evMilesPerWeek", evMilesPerWeek);
    storeNumber("busMilesPerWeek", busMilesPerWeek);
    storeNumber("trainMilesPerWeek", trainMilesPerWeek);
    storeNumber("flightHoursPerYear", flightHoursPerYear);

    foodValues.forEach((item) => {
      storeNumber(item[0], item[2]);
    });

    storeNumber("cookedMealsPerWeek", cookedMealsPerWeek);
    storeNumber("homeSquareFeet", homeSquareFeet);
    storeNumber(
      "homeDegreesAdjustedDownInWinter",
      homeDegreesAdjustedDownInWinter
    );
    storeNumber("lightbulbQuantity", lightbulbQuantity);
    storeNumber("lightBulbHoursPerDay", lightBulbHoursPerDay);
    storeNumber("computerHoursPerDay", computerHoursPerDay);
    storeNumber("dishwasherUsesPerMonth", dishwasherUsesPerMonth);
    storeNumber("clothesWasherUsesPerMonth", clothesWasherUsesPerMonth);
    storeNumber("clothesDryerUsesPerMonth", clothesDryerUsesPerMonth);
    storeNumber("fridgeQuantity", fridgeQuantity);
    storeNumber("gallonsOfHotWaterUsedPerDay", gallonsOfHotWaterUsedPerDay);

    purchaseValues.forEach((item) => {
      storeNumber(item[0], item[2]);
    });

    history.pushState(null, null, url);
  }

  onMount(() => {
    // // get url params
    // let url = new URL(window.location.href);

    // let loaded = { ...parameters };

    // for (var key in parameters) {
    //   var parameter = url.searchParams.get(key);
    //   loaded[key] = parameter;
    // }
    // parameters = loaded;

    onPrimaryColorChange();
    onSecondaryColorChange();
  });
</script>

<main
  style="--color-primary: {colorPrimary}; --color-secondary: {colorSecondary};"
>
  <div id="site-container">
    {#if !why}
      <div id="top-right">
        <button on:click={() => editing.update((e) => !e)}>
          {#if $editing}Done editing{:else}Edit recipe{/if}
        </button>
      </div>
    {/if}

    <div id="bottom-left">
      <button
        on:click={() => {
          why = !why;
          editing.update(() => false);
        }}
      >
        {#if why}Okay cool{:else}Why?{/if}
      </button>
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

          <!-- TRANSPORTATION -->

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
            title="EV miles per week"
            bind:inputValue={evMilesPerWeek}
            outputValue={evKgPerYear}
            indentLevel={1}
            note="The average Tesla gets .25kwh per mile"
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

          <!-- food -->

          <SectionTitle
            title="Food"
            detail="Enter servings per week"
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

          <!-- home -->

          <SectionTitle title="Home" total={homeKgPerYear} />
          <LineItem
            title="Square feet"
            detail="Divide if you have roommates"
            bind:inputValue={homeSquareFeet}
            indentLevel={1}
          />
          <LineItem
            title="Degrees adjusted down in winter"
            bind:inputValue={homeDegreesAdjustedDownInWinter}
            indentLevel={1}
          />

          <!-- electricity -->

          <SectionTitle title="Electricity" total={totalElectricityKgPerYear} />
          <LineItem
            title="Lightbulbs in home"
            detail="Divide if you have roommates"
            bind:inputValue={lightbulbQuantity}
            indentLevel={1}
          />
          <LineItem
            title="Lights hours per day"
            bind:inputValue={lightBulbHoursPerDay}
            outputValue={lightBulbKgPerYear}
            indentLevel={2}
          />
          <LineItem
            title="Laptop hours per day"
            bind:inputValue={computerHoursPerDay}
            outputValue={computerKgPerYear}
            indentLevel={1}
          />
          <LineItem
            title="Dishwasher uses per month"
            bind:inputValue={dishwasherUsesPerMonth}
            outputValue={dishwasherKgPerYear}
            indentLevel={1}
          />
          <LineItem
            title="Clothes washer uses per month"
            bind:inputValue={clothesWasherUsesPerMonth}
            outputValue={clothesWasherKgPerYear}
            indentLevel={1}
          />
          <LineItem
            title="Clothes dryer uses per month"
            bind:inputValue={clothesDryerUsesPerMonth}
            outputValue={clothesDrykerKgPerYear}
            indentLevel={1}
          />
          <LineItem
            title="Fridge quantity"
            bind:inputValue={fridgeQuantity}
            outputValue={fridgeKgPerYear}
            indentLevel={1}
          />

          <!-- water -->

          <LineItem
            title="Gallons of hot water per day"
            bind:inputValue={gallonsOfHotWaterUsedPerDay}
            outputValue={hotWaterKgPerYear}
            indentLevel={0}
          />

          <!-- purchases -->

          <SectionTitle
            title="Purchases"
            detail="Made within the entire year"
            total={purchaseKgPerYear}
          />

          {#each purchaseValues as item}
            <LineItem
              title={item[0]}
              bind:inputValue={item[2]}
              outputValue={item[3]}
              indentLevel={1}
              note="{item[1]}kg CO2e per item"
            />
          {/each}

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
            <div class="color-container">
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
            </div>
          </div>
        {/if}
      </div>
    {/if}

    {#if why}
      <div id="why-container">
        <div id="why-container-inner">
          <h2>
            CO2 emissions are kind of like calories. They don’t represent
            overall health, but if you’re struggling with your weight, it’s a
            good place to start. On earth, CO2 emissions contribute to a buildup
            of greenhouse gases. Greenhouse gases trap heat, preventing it from
            dissipating into the atmosphere.
          </h2>
          <h2>
            We have been cranking out a staggeringly massive amount of CO2 in
            the last hundred years. All living things on this planet have
            handled gradual changes very well but quick changes to the
            environment are difficult to deal with. Curbing CO2 emissions to the
            EPA’s recommended 2.5 tons a year is one of the most effective
            things we can do to ensure our future.
          </h2>
          <p id="why-note">
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
    /* this is because the background color actually doesn't update correctly */
    background-color: black;
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

  #top-right {
    position: fixed;
    top: 0.5em;
    right: 0.5em;
  }
  #bottom-left {
    position: fixed;
    bottom: 0.5em;
    left: 0.5em;
  }
  .title {
    width: 100%;
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
    margin-top: 4rem;
    margin-bottom: 4rem;
    border: 2px solid var(--color-primary);
    padding: 0.5em;
  }

  #content-container {
    margin-left: auto;
    margin-right: auto;
    padding: 0.5rem;
    max-width: var(--width);
  }

  #why-container {
    width: 100%;
    height: 100vh;
    display: grid;
    place-items: center;
  }

  #why-container-inner {
    max-width: var(--width);
    padding: 0.5rem;
  }

  #why-container-inner p,
  #why-container-inner h2 {
    text-align: center;
    margin-bottom: 0.8em;
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
