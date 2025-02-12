<script>
  // @ts-nocheck
  import { onMount } from "svelte";
  import * as d3 from "d3";

  const endPoint = { x: 1300, y: 400 };
  const yAxis_length = 700;

  /**
   * @type {SVGSVGElement|null}
   */
  export let barchart;
  let container;
  export const margin = { top: 0, right: 100, left: 30, bottom: 20 };
  export const width = 1000 - margin.left - margin.right;
  export const height = 700 - margin.top - margin.bottom;

  const data = [
    { period: "< 30 Days", count: 5351 },
    { period: "30-90 Days", count: 8498 },
    { period: "90 Days-6 Months", count: 12527 },
    { period: "6 Months-1 Year", count: 19221 },
    { period: "1-2 Years", count: 25541 },
    { period: "2-3 Years", count: 15725 },
    { period: "3-5 Years", count: 15958 },
    { period: "5+ Years", count: 11983 },
  ];

  onMount(() => {
    // Create SVG
    const svg = d3
      .select(barchart)
      .append("svg")
      .attr("width", "100%")
      .attr("height", "100%")
      .attr(
        "viewBox",
        `0 0 ${width + margin.left + margin.right} ${height + margin.top + margin.bottom}`
      )
      .append("g")
      .attr("transform", `translate(${margin.left},${margin.top})`);

    // Append marker definition to the SVG
    svg
      .append("defs")
      .append("marker")
      .attr("id", "arrow")
      .attr("viewBox", "0 0 10 10")
      .attr("refX", 6)
      .attr("refY", 5)
      .attr("markerWidth", 6)
      .attr("markerHeight", 6)
      .attr("orient", "auto-start-reverse")
      .append("path")
      .attr("d", "M 0 0 L 10 5 L 0 10 z")
      .style("fill", "#5c0c06");

    // Y scale (for periods)
    const y = d3
      .scaleBand()
      .range([0, height])
      .domain(data.map((d) => d.period))
      .padding(0.4);

    // X scale (for counts)
    const x = d3
      .scaleLinear()
      .range([width, 0])
      .domain([0, d3.max(data, (d) => d.count)]);

    // Add X axis
    // svg.append("g").call(d3.axisTop(x).tickFormat(d3.format(",")));
    // .selectAll("text")
    // .style("text-anchor", "start")
    // .attr("dx", "0em")
    // .attr("dy", "0.9em")
    // .attr("transform", "rotate(-45)");

    // Create tooltip
    const tooltip = d3
      .select(container)
      .append("div")
      .attr("class", "tooltip")
      .style("opacity", 0)
      .style("position", "absolute")
      .style("background-color", "white")
      .style("border", "1px solid #ddd")
      .style("padding", "10px")
      .style("border-radius", "4px");

    // Add bars
    svg
      .selectAll(".layer1")
      .data(data)
      .join("rect")
      .attr("y", (d) => y(d.period))
      .attr("x", (d) => x(d.count))
      .attr("height", y.bandwidth())
      .attr("width", (d) => width - x(d.count))
      // .attr("rx", 20)
      .attr("fill", "url(#gradient)");

    svg
      .selectAll(".layer2")
      .data(data)
      .join("rect")
      .attr("y", (d) => y(d.period))
      .attr("x", (d) => x(d.count))
      .attr("height", y.bandwidth())
      .attr("width", (d) => width - x(d.count))
      .attr("filter", "url(#gggrain-filter)")
      .style("mix-blend-mode", "soft-light")
      .style("opacity", 0.9)
      .on("mouseover", (event, d) => {
        tooltip.transition().duration(200).style("opacity", 0.8);
        tooltip
          .html(`${d.period}: ${d3.format(",")(d.count)}`)
          .style("left", `${event.pageX + 10}px`)
          .style("top", `${event.pageY - 28}px`);
      })
      .on("mouseout", () => {
        tooltip.transition().duration(500).style("opacity", 0);
      });

    // add value labels
    svg
      .selectAll(".value-label")
      .data(data)
      .join("text")
      .attr("class", "value-label")
      .attr("x", (d) => x(d.count) + 5)
      .attr("y", (d) => y(d.period) + y.bandwidth() / 2)
      .attr("dy", "0.35em")
      .text((d) => d3.format(",")(d.count))
      .style("font-size", "12px")
      .style("fill", "#f8db01");

    // Add Y axis
    svg
      .append("g")
      .attr("transform", `translate(${width},0)`)
      .attr("id", `y-axis`)
      .call(d3.axisRight(y).tickSize(0))
      .selectAll("text")
      .style("text-anchor", "end")
      .attr("dx", "-1em")
      .attr("dy", "2.8em")
      .attr("font-family", "Roboto Slab")
      .style("font-size", "13px")
      .style("fill", "#5c0c06");

    d3.select("#y-axis")
      .select("path")
      .style("stroke", "#5c0c0699")
      .style("stroke-width", "1.2px")
      .attr("marker-end", "url(#arrow)");

    // Handle resize
    const resize = () => {
      const newWidth = barchart.clientWidth - margin.left - margin.right;
      x.range([0, newWidth]);
      svg.select(".x-axis").call(d3.axisBottom(x));
    };

    window.addEventListener("resize", resize);

    return () => {
      window.removeEventListener("resize", resize);
    };
  });
</script>

<div bind:this={container}>
  <svg class="main-chart" id="main-chart" bind:this={barchart}>
    <defs>
      <!-- Define Linear Gradient -->
      <linearGradient id="gradient">
        <stop offset="0%" stop-color="#5c0c06" />
        <stop offset="100%" stop-color="#B3353509" />
      </linearGradient>

      <filter
        id="gggrain-filter"
        x="0%"
        y="0"
        width="100%"
        height="100%"
        filterUnits="objectBoundingBox"
        primitiveUnits="userSpaceOnUse"
        color-interpolation-filters="sRGB"
      >
        <feTurbulence
          type="fractalNoise"
          baseFrequency="0.7"
          numOctaves="2"
          seed="2"
          stitchTiles="stitch"
          x="0%"
          y="0%"
          width="100%"
          height="100%"
          result="turbulence"
        ></feTurbulence>
        <feColorMatrix
          type="saturate"
          values="0"
          x="0%"
          y="0%"
          width="100%"
          height="100%"
          in="turbulence"
          result="colormatrix"
        ></feColorMatrix>
        <feComponentTransfer
          x="0%"
          y="0%"
          width="100%"
          height="100%"
          in="colormatrix"
          result="componentTransfer"
        >
          <feFuncR type="linear" slope="2"></feFuncR>
          <feFuncG type="linear" slope="2"></feFuncG>
          <feFuncB type="linear" slope="2"></feFuncB>
          <feFuncA type="linear" slope="0.95"></feFuncA>
        </feComponentTransfer>
        <feColorMatrix
          x="0%"
          y="0%"
          width="100%"
          height="100%"
          in="componentTransfer"
          result="colormatrix2"
          type="matrix"
          values="1 0 0 0 0
            0 1 0 0 0
            0 0 1 0 0
            0 0 0 17 -9"
        ></feColorMatrix>
      </filter>
    </defs>
  </svg>

  <svg class="bg-gradient">
    <defs>
      <linearGradient
        gradientTransform="rotate(-180, 0.5, 0.5)"
        x1="50%"
        y1="0%"
        x2="50%"
        y2="100%"
        id="bg-gradient"
        ><stop stop-color="#F8DB0122" stop-opacity="1" offset="-0%"></stop><stop
          stop-color="#E4E1DB33"
          stop-opacity="0"
          offset="100%"
        ></stop></linearGradient
      >
      <filter
        id="bg-grain-filter"
        x="0%"
        y="0"
        width="100%"
        height="100%"
        filterUnits="objectBoundingBox"
        primitiveUnits="userSpaceOnUse"
        color-interpolation-filters="sRGB"
      >
        <feTurbulence
          type="fractalNoise"
          baseFrequency="0.6"
          numOctaves="2"
          seed="2"
          stitchTiles="stitch"
          x="0%"
          y="0%"
          width="100%"
          height="100%"
          result="turbulence"
        ></feTurbulence>
        <feColorMatrix
          type="saturate"
          values="2s"
          x="0%"
          y="0%"
          width="100%"
          height="100%"
          in="turbulence"
          result="colormatrix"
        ></feColorMatrix>
        <feComponentTransfer
          x="0%"
          y="0%"
          width="100%"
          height="100%"
          in="colormatrix"
          result="componentTransfer"
        >
          <feFuncR type="linear" slope="3"></feFuncR>
          <feFuncG type="linear" slope="3"></feFuncG>
          <feFuncB type="linear" slope="3"></feFuncB>
          <feFuncA type="linear" slope="0.75"></feFuncA>
        </feComponentTransfer>
        <feColorMatrix
          x="0%"
          y="0%"
          width="100%"
          height="100%"
          in="componentTransfer"
          result="colormatrix2"
          type="matrix"
          values="1 0 0 0 0
            0 1 0 0 0
            0 0 1 0 0
            0 0 0 17 -9"
        ></feColorMatrix>
      </filter>
    </defs>

    <rect width="100%" height="100%" fill="url(#bg-gradient)"></rect>
    <rect
      width="100%"
      height="100%"
      fill="transparent"
      filter="url(#bg-grain-filter)"
    ></rect>
  </svg>
</div>

<style>
  svg {
    width: 100vw;
    height: 85vh;
    position: absolute;
    left: 0;
    /* border: 1px solid black; */
  }

  .main-chart {
    width: 100vw;
    z-index: 2000;
    /* margin-left: 10rem; */
    font-family: "Roboto Slab", serif;
    /* border: 1px solid black; */
  }

  :global(.tooltip) {
    pointer-events: none;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    font-size: 0.875rem;
    z-index: 10000;
  }

  .bg-gradient {
    position: absolute;
    top: 100vh;
    height: 120vh;
    z-index: -1;
  }
</style>
