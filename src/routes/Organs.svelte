<script>
  // @ts-nocheck
  import * as d3 from "d3";
  import { onMount } from "svelte";

  const orangeScale = [
    "#B58A4C",
    "#D0A76F",
    "#DEB985",
    "#F0CB96",
    "#F5D4A6",
    "#F9DDB5",
  ];
  const greyScale = ["#aaa", "#bbb", "#ccc", "#d7d7d7", "#ddd", "#f0f0f0"];
  const years = Array.from({ length: 38 }, (_, i) => 1988 + i);

  let startYear = 1988;
  let endYear = 2025;
  $: [minYear, maxYear] = [
    Math.min(startYear, endYear),
    Math.max(startYear, endYear),
  ];
  $: startDate = new Date(minYear, 0, 1);
  $: endDate = maxYear === 2025 ? new Date() : new Date(maxYear, 11, 31);
  $: diffInDays = Math.floor((endDate - startDate) / (1000 * 60 * 60 * 24));

  let selectedOrgan = null;
  // "Kidney";
  let OrganData = [];
  let loadingData = true;

  onMount(async () => {
    OrganData = await d3.csv("/data/Organ_by_Transplant_Year.csv");
    loadingData = false;

    // initialize everything
    organs = ["Kidney", "Liver", "Heart", "Lung", "Pancreas", "Intestine"].map(
      (name, i) => ({
        name,
        percentage: getTransplantPercent(startYear, endYear, name),
        width: [52, 21, 9, 5, 2, 2][i],
      })
    );
    transplantNum = new Intl.NumberFormat("en-US").format(
      getTransplantNum(startYear, endYear, selectedOrgan)
    );
    transplantTotal = new Intl.NumberFormat("en-US").format(
      getTransplantNum(startYear, endYear, "AllOrgans")
    );
    transplantAverage = new Intl.NumberFormat("en-US").format(
      (getTransplantNum(startYear, endYear, "AllOrgans") / diffInDays).toFixed()
    );
  });

  function getBarHeight(percentage) {
    return Math.max(0, percentage * 13) + "px";
  }

  function getTransplantNum(startYr, endYr, organName) {
    if (loadingData || OrganData.length === 0) return 0;

    const [minYear, maxYear] = [
      Math.min(startYr, endYr),
      Math.max(startYr, endYr),
    ];
    const relevantData = OrganData.slice(minYear - 1988, maxYear - 1988 + 1);

    return relevantData.reduce(
      (sum, year) => sum + (parseInt(year[organName]) || 0),
      0
    );
  }
  function getTransplantPercent(startYr, endYr, organ) {
    return (
      (100 * getTransplantNum(startYr, endYr, organ)) /
      getTransplantNum(startYr, endYr, "AllOrgans")
    ).toFixed(1);
  }

  $: transplantNum = new Intl.NumberFormat("en-US").format(
    getTransplantNum(startYear, endYear, selectedOrgan)
  );
  $: transplantTotal = new Intl.NumberFormat("en-US").format(
    getTransplantNum(startYear, endYear, "AllOrgans")
  );

  $: transplantAverage = new Intl.NumberFormat("en-US").format(
    (getTransplantNum(startYear, endYear, "AllOrgans") / diffInDays).toFixed()
  );
  $: transplantPercent = getTransplantPercent(
    startYear,
    endYear,
    selectedOrgan
  );

  $: organs = ["Kidney", "Liver", "Heart", "Lung", "Pancreas", "Intestine"].map(
    (name, i) => ({
      name,
      percentage: getTransplantPercent(startYear, endYear, name),
      width: [52, 21, 9, 5, 2, 2][i],
    })
  );

  $: innerWidth = 0;
  $: innerHeight = 0;
</script>

<svelte:window bind:innerWidth bind:innerHeight />

<section class="data-notes-1">
  Between
  <div class="dropdown">
    <select bind:value={startYear}>
      {#each years as year}
        <option value={year}>{year}</option>
      {/each}
    </select>
  </div>

  and
  <div class="dropdown">
    <select bind:value={endYear}>
      {#each years as year}
        <option value={year}>{year}</option>
      {/each}
    </select>,
  </div>
  a total of
  <span class="text-large">{transplantTotal}</span>&nbsp;organ transplants were
  performed, averaging <b>{transplantAverage}</b> per day.
</section>

<section class="grid-container">
  {#each organs as organ, index}
    <div
      role="button"
      class="grid-item"
      style="width: {innerWidth
        ? innerWidth * (organ.width / 140) + 100
        : 200}px;"
      tabindex="0"
      on:mouseover={() => {
        selectedOrgan = organ.name;
      }}
      on:focus={() => {
        selectedOrgan = organ.name;
      }}
    >
      <svg
        class="organ-bar"
        width="100%"
        height={getBarHeight(organ.percentage)}
      >
        <rect
          class="organ-rect"
          x="0"
          y="0"
          width="105%"
          height="100%"
          fill={orangeScale[index]}
          style=" filter: brightness( {selectedOrgan === organ.name
            ? '1.15'
            : '1'});
          filter: saturate({selectedOrgan === organ.name ? '1' : '0.2'});"
        />
      </svg>
      <img
        class="illustration"
        src={"/anatomy/" + organ.name.toLocaleLowerCase() + ".png"}
        alt={organ.name}
        width="100%"
        style="margin-top: {Math.max(
          0,
          organ.name === 'Kidney' ? 50 : 350 - 3.5 * organ.width
        )}px;
          filter: brightness( {selectedOrgan === organ.name ? '1.15' : '1'});
          filter: saturate({selectedOrgan === organ.name ? '1.2' : '0.6'});"
      />
    </div>
  {/each}
</section>

<section class="data-notes-2">
  <br />
  {#if selectedOrgan}
    Of these,
    <span class="text-large">{selectedOrgan}</span>&nbsp;transplants accounted
    for
    <span class="text-large">{transplantPercent}%</span>
    &#40;{transplantNum}&#41; <br />of all procedures during this time period.
  {/if}
</section>

<style>
  .grid-container {
    display: flex;
    flex-wrap: nowrap;
    /* gap: clamp(10px, 5vw, 65px); */
    justify-content: center;
    align-items: flex-start;
    margin-top: 10px;
    padding: 0 15px;
    z-index: 100;
  }

  .grid-item {
    display: flex;
    flex-direction: column;
    align-items: center;
    text-align: center;
    position: relative;
    cursor: pointer;
    transition: all 0.2s ease-in;
  }

  .organ-bar {
    position: absolute;
    align-self: start;
    top: 0;
    left: -10px;
    z-index: -1;
    transition: all 0.3s ease;
  }

  .illustration {
    position: relative;
    margin-bottom: 10px;
    display: block;
    align-self: center;
    z-index: 0;
    transition: all 0.2s ease-in;
  }

  .data-notes-2 {
    font-size: 18px;
    text-align: right;
    width: 70vw;
    display: block;
    margin-left: auto;
    margin-right: 60px;
    line-height: 35px;
    margin-top: -10vw;
    z-index: 5;
  }

  .data-notes-1 {
    font-size: 18px;
    display: block;
    text-align: right;
    margin-top: 2rem;
    margin-right: 10px;
    margin-bottom: 0;
    z-index: 5;
  }

  .dropdown {
    position: relative;
    display: inline-block;
  }

  .dropdown select {
    appearance: none;
    -webkit-appearance: none;
    -moz-appearance: none;
    background: none;
    border: 0px;
    padding: 0px 30px 0px 0px;
    font-size: 40px;
    font-weight: 500;
    font-family: "Roboto Slab", serif;
    color: var(--color-text);
    cursor: pointer;
    outline: none;
    transition: text-shadow 0.2s ease-in;
    /* border: 1px solid green; */
  }

  .dropdown option {
    font-size: 20px;
    scrollbar-width: none; /* Hides scrollbar in Firefox */
  }

  .dropdown::after {
    content: "▼";
    font-size: 22px;
    color: var(--color-text);
    position: absolute;
    right: 5px;
    top: 50%;
    transform: translateY(-50%) rotate(0deg);
    cursor: pointer;
    pointer-events: none;
    /* border: 1px solid red; */
  }

  .dropdown select:hover {
    text-shadow: 0 0 10px #758d867a;
  }
  .dropdown select::-webkit-scrollbar {
    display: none;
  }

  .organ-rect {
    transition: all 0.2s ease-in;
  }
</style>
