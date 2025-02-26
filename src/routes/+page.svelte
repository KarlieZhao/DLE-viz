<script lang="js">
  import { onMount } from "svelte";
  import Nav from "./Nav.svelte";
  import Organs from "./Organs.svelte";
  import YearViz from "./YearViz.svelte";
  /**
   * @type {any[]}
   */
  let words = [];

  /**
   * @type {{
   * start: {x:number; y: number};
   * end: {x:number; y: number};
   * control1:{x:number; y: number};
   * control2:{x:number; y: number};
   * }[]}
   */

  /** @type {HTMLElement | null} */
  let waitlist = null;
  /** @type {HTMLElement | null} */
  let waste = null;
  /** @type {HTMLElement | null} */
  let inequity = null;
  /** @type {SVGSVGElement | null} */
  let barChart = null;
  /** @type {SVGSVGElement | null} */
  let svgTrails = null;
  let wordPositions = [];
  let barchartWidth = 0;

  // svg lines
  //   const renderTrails = () => {
  //     if (words.length > 0 && barChart) {
  //       const endPoint = barChart
  //         .querySelector("#y-axis")
  //         ?.getBoundingClientRect();
  //       const endX = endPoint
  //         ? endPoint.x + endPoint.width - 6
  //         : barChart.getBoundingClientRect().x;
  //       const endY = 300;
  //       svgTrails?.setAttribute("style", `height: ${endY}px`);
  //       barChart.setAttribute("style", `margin-top: ${endY}px`);

  //       wordPositions = words.map((word, index) => {
  //         const rect = word.getBoundingClientRect();
  //         const startX = rect.left + rect.width / 2;
  //         const startY = rect.top + window.scrollY;

  //         // start control point
  //         const controlX1 = (startX + endX) / 2;
  //         const controlY1 = startY + endY / 2 - 500 - index * 100; // offset for curvature

  //         // end control point
  //         const controlX2 = endX - 30;
  //         const controlY2 = endY - 200;
  //         return {
  //           start: { x: startX, y: startY },
  //           end: { x: endX, y: endY },
  //           control1: { x: controlX1, y: controlY1 },
  //           control2: { x: controlX2, y: controlY2 },
  //         };
  //       });
  //     }
  //   };
  //   onMount(() => {
  //     words = [waitlist, waste, inequity].filter(Boolean);
  //     renderTrails();

  //     window.addEventListener("resize", renderTrails);
  //     return () => {
  //       window.removeEventListener("resize", renderTrails);
  //     };
  //   });
</script>

<svelte:head>
  <title>Organ Tranplant</title>
  <meta name="description" content="data viz" />
</svelte:head>

<main>
  <div class="cover-video-backdrop">
    <img class="cover-video" src="/cover.png" alt="" />

    <h1 class="main-title serif">
      A History of <br />
      <span bind:this={waitlist}>Long Waitlists</span>,
      <span bind:this={waste}>Waste</span>, and
      <span bind:this={inequity}>Inequity</span>
    </h1>
    <!-- <div class="about-tag serif">About</div> -->
  </div>

  <Nav />

  <!-- transplant performed dataviz   -->
  <Organs />

  <!-- waitlist dataviz -->
  <!-- <section>
    <div class="intro">
      As of Dec. 31, 2024, there are <span class="text-large">104,197</span>
      persons on the national transplant waiting list.
    </div>
    <div class="details">
      <span class="text-mid" id="wl-count">19,211</span> persons have been
      waiting for
      <span class="text-mid" id="wl-period">6 month - 1 year</span>.

      <br /> <br />
      Among which,
      <span class="text-mid">3,872</span> persons are waiting for
      <span class="text-mid">kidney</span> transplantation.
    </div>
    <YearViz bind:barchart={barChart} bind:width={barchartWidth} />
    <svg class="curves" bind:this={svgTrails} width="100%" height="100%">
      {#each wordPositions as { start, end, control1, control2 }}
        <path
          d={`M ${start.x} 0 C ${control1.x} ${control1.y}, ${control2.x} ${control2.y}, ${end.x} ${end.y}`}
          fill="transparent"
        />
      {/each}
    </svg>
  </section> -->
</main>

<style>
  main {
    overflow: visible;
    height: 300vh;
    scrollbar-width: 0;
  }

  main::-webkit-scrollbar {
    width: 0px;
  }
  section {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    flex: 0.6;
  }

  svg {
    width: 100vw;
    min-height: 200px;
    height: max-content;
    position: absolute;
    left: 0;
    /* border: 1px solid black; */
  }

  path {
    /* stroke: #000; */
    stroke: #5c0c0677;
    stroke-dasharray: 8px, 8px;
    stroke-width: 1.5;
    fill: none;
  }

  .intro {
    font-size: 16px;
    text-align: left;
    align-self: start;
    width: 30vw;
    position: absolute;
    margin: 3rem;
    top: 105vh;
  }

  .details {
    font-size: 16px;
    text-align: left;
    align-self: start;
    position: absolute;
    margin-left: 3rem;
    top: 150vh;
  }
  .text-large {
    font-size: 40px;
    font-weight: 500;
  }

  .text-mid {
    font-size: 28px;
    font-weight: 400;
  }
  .cover-video-backdrop {
    height: fit-content;
    width: fit-content;
    height: 100vh;
    background: rgb(0, 0, 0);
    background: linear-gradient(
      0deg,
      rgba(0, 0, 0, 0) 0%,
      rgba(0, 0, 0, 1) 15%
    );
  }

  .cover-video {
    height: 92vh;
    width: 100vw;
    object-fit: cover;
    clip: auto;
    -webkit-mask-image: linear-gradient(
      0deg,
      rgba(0, 0, 0, 0.3) 0%,
      rgba(0, 0, 0, 1) 10%
    );
    mask-image: linear-gradient(
      0deg,
      rgba(0, 0, 0, 0.3) 0%,
      rgba(0, 0, 0, 1) 10%
    );
  }

  .main-title {
    position: absolute;
    bottom: 0;
    font-size: 5rem;
    line-height: 5.5rem;
    color: #f8db01;
    text-align: left;
    margin-left: 2rem;
    margin-bottom: 1rem;
    filter: drop-shadow(1px 5px 5px #0006);
  }

  .about-tag {
    z-index: 1000;
    position: fixed;
    top: 20px;
    right: 20px;
    color: #8b8343;
    /* #f8db01; */
    font-size: 1.2rem;
    filter: drop-shadow(1px 5px 5px #0006);
    mix-blend-mode: screen;
  }
</style>
