<script>
  // @ts-nocheck

  let organs = [
    { name: "kidney", number: 0, dimension: "600px", percentage: 70 },
    { name: "liver", number: 0, dimension: "550px", percentage: 35 },
    { name: "heart", number: 0, dimension: "250px", percentage: 22 },
    { name: "lungs", number: 0, dimension: "280px", percentage: 18 },
    { name: "pancreas", number: 0, dimension: "110px", percentage: 10 },
    { name: "intestine", number: 0, dimension: "90px", percentage: 5 },
  ];

  let years = Array.from({ length: 2025 - 1988 + 1 }, (_, i) => 1988 + i);
  let startYear = 1988;
  let endYear = 2025;
  let selectedOrgan = "kidney";

  function getBarHeight(percentage) {
    return Math.max(20, percentage * 9.5) + "px";
  }
</script>

<div>
  <section class="grid-container mt-40">
    {#each organs as organ}
      <div
        role="button"
        class="grid-item"
        style="width: {organ.dimension};"
        tabindex="0"
        on:mouseover={() => (selectedOrgan = organ.name)}
        on:focus={() => (selectedOrgan = organ.name)}
      >
        <svg
          class="organ-bar"
          width="100%"
          height={getBarHeight(organ.percentage)}
        >
          <rect
            x="0"
            y="0"
            width="100%"
            height="100%"
            fill={selectedOrgan === organ.name ? "#C5995A" : "#DEB985"}
          />
        </svg>
        <img
          class="illustration"
          src={"/anatomy/" + organ.name + ".png"}
          alt={organ.name}
          width="100%"
        />
      </div>
    {/each}
  </section>

  <section class="data-notes">
    <div>
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
        </select>
      </div>

      <br />
      there were <span class="large-text">600,148 </span>
      <span class="large-text">kidney</span> transplant performed in the U.S,
      <br />accounting for 76% of all transplants during the time period.

      <!-- {#if selectedOrgan}
        {#each (organ = organs.find((o) => o.name === selectedOrgan))}
          <span class="large-text">{organ.number.toLocaleString()} </span>
          <span class="large-text">{organ.name}</span> transplants performed in
          the U.S,
          <br />accounting for {organ.percentage}% of all transplants during the
          time period.
        {/each}
      {/if} -->
    </div>
  </section>
</div>

<style>
  .grid-container {
    display: flex;
    flex-wrap: nowrap;
    /* gap: clamp(10px, 5vw, 65px); */
    justify-content: center;
    /* align-items: center; */
    align-items: flex-start;
    padding: 30px;
  }

  .grid-item {
    display: flex;
    flex-direction: column;
    align-items: center;
    text-align: center;
    position: relative;
    cursor: pointer;
  }

  .organ-bar {
    position: absolute;
    top: 0;
    left: -10px;
    z-index: 1;
    transition: fill 0.3s ease;
  }

  .illustration {
    position: relative;
    margin-bottom: 10px;
    z-index: 2;
  }

  .data-notes {
    font-size: 20px;
    text-align: right;
    width: 70vw;
    display: block;
    margin-left: auto;
    margin-right: 60px;
    line-height: 35px;
  }

  .large-text {
    font-size: 60px;
    line-height: 40px;
  }

  .mt-40 {
    margin-top: 100px;
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
    padding: 8px 30px 8px 10px;
    font-size: 20px;

    font-family: "Roboto Slab", serif;
    color: #273548;
    cursor: pointer;
    outline: none;
    transition: text-shadow 0.2s ease-in;

    scrollbar-width: none; /* Hides scrollbar in Firefox */
  }

  .dropdown::after {
    content: "▲";
    color: #273548;
    position: absolute;
    right: 10px;
    top: 50%;
    transform: translateY(-50%) rotate(0deg);
    pointer-events: none;
  }

  .dropdown select:hover {
    text-shadow: 0 0 10px #758d867a;
  }
  .dropdown select::-webkit-scrollbar {
    display: none;
  }
</style>
