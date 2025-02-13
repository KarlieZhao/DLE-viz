<script>
  // @ts-nocheck
  import { onMount } from "svelte";
  import * as d3 from "d3";

  const endPoint = { x: 1300, y: 400 };
  const mainChartWidth = 900;

  /**
   * @type {SVGSVGElement|null}
   */
  export let barchart;
  let container;
  export const margin = { top: 0, right: 100, left: 0, bottom: 20 };
  export const width = mainChartWidth - margin.left - margin.right;
  export const height = mainChartWidth * 0.7 - margin.top - margin.bottom;

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

  const data2D = [
    {
      period: "All Time",
      allOrgans: 104301,
      kidney: 90088,
      liver: 9243,
      pancreas: 843,
      kidneyPancreas: 2237,
      heart: 3592,
      lung: 948,
      heartLung: 42,
      intestine: 184,
    },
    {
      period: "< 30 Days",
      allOrgans: 5374,
      kidney: 3872,
      liver: 933,
      pancreas: 39,
      kidneyPancreas: 128,
      heart: 379,
      lung: 209,
      heartLung: 6,
      intestine: 10,
    },
    {
      period: "30-90 Days",
      allOrgans: 7912,
      kidney: 6154,
      liver: 1158,
      pancreas: 53,
      kidneyPancreas: 206,
      heart: 392,
      lung: 195,
      heartLung: 7,
      intestine: 16,
    },
    {
      period: "90 Days-6 Months",
      allOrgans: 11949,
      kidney: 9694,
      liver: 1483,
      pancreas: 94,
      kidneyPancreas: 313,
      heart: 499,
      lung: 207,
      heartLung: 6,
      intestine: 20,
    },
    {
      period: "6 Months-1 Year",
      allOrgans: 18384,
      kidney: 15619,
      liver: 1779,
      pancreas: 145,
      kidneyPancreas: 512,
      heart: 689,
      lung: 156,
      heartLung: 9,
      intestine: 37,
    },
    {
      period: "1-2 Years",
      allOrgans: 24282,
      kidney: 21492,
      liver: 1693,
      pancreas: 198,
      kidneyPancreas: 585,
      heart: 727,
      lung: 114,
      heartLung: 7,
      intestine: 34,
    },
    {
      period: "2-3 Years",
      allOrgans: 15088,
      kidney: 13821,
      liver: 767,
      pancreas: 110,
      kidneyPancreas: 283,
      heart: 334,
      lung: 38,
      heartLung: 2,
      intestine: 20,
    },
    {
      period: "3-5 Years",
      allOrgans: 15279,
      kidney: 14057,
      liver: 768,
      pancreas: 86,
      kidneyPancreas: 183,
      heart: 360,
      lung: 21,
      heartLung: 3,
      intestine: 21,
    },
    {
      period: "5+ Years",
      allOrgans: 11625,
      kidney: 10520,
      liver: 758,
      pancreas: 124,
      kidneyPancreas: 93,
      heart: 243,
      lung: 10,
      heartLung: 2,
      intestine: 29,
    },
  ];

  onMount(() => {
    const getDetailData = (d) => {
      const selectedRow = data2D.find((entry) => entry.period === d.period);
      if (selectedRow) {
        return Object.entries(selectedRow)
          .filter(([key]) => key != "period" && key != "allOrgans")
          .map(([key, value]) => ({ subCategory: key, count: value }))
          .sort((a, b) => b.count - a.count);
      }
      return d;
    };

    // Create SVG
    const svg = d3
      .select(barchart)
      .append("svg")
      .attr("width", "100%")
      .attr("height", "100%")
      // .attr("width", `${mainChartWidth * 2}`)
      // .attr("height", `${mainChartWidth * 0.7}`)
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
      .padding(0.3);

    // X scale (for counts)
    const x = d3
      .scaleLinear()
      .range([width, 0])
      .domain([0, d3.max(data, (d) => d.count)]);

    // Add layer1 gradient bars
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

    // Add layer2 highlight gradient bars
    svg
      .selectAll(".layer2")
      .data(data)
      .join("rect")
      .attr("y", (d) => y(d.period))
      .attr("x", (d) => x(d.count))
      .attr("class", (d) => `bar-${d.period.replace(/[^a-zA-Z0-9]/g, "-")}`)
      .attr("height", y.bandwidth())
      .attr("width", (d) => width - x(d.count))
      // .attr("rx", 20)
      .attr("fill", "url(#highlight)")
      .style("opacity", 0);

    // Add layer3 noise
    svg
      .selectAll(".layer3")
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
        d3.select(`#wl-count`).text(d3.format(",")(d.count));
        d3.select(`#wl-period`).text(d.period.toLowerCase());

        d3.select(`.label-${d.period.replace(/[^a-zA-Z0-9]/g, "-")}`)
          .transition()
          .duration(200)
          .style("opacity", 1);

        d3.select(`.bar-${d.period.replace(/[^a-zA-Z0-9]/g, "-")}`)
          .transition()
          .duration(200)
          .style("opacity", 1);
      })
      .on("mouseout", (event, d) => {
        d3.select(`.label-${d.period.replace(/[^a-zA-Z0-9]/g, "-")}`)
          .transition()
          .duration(200)
          .style("opacity", 0);

        d3.select(`.bar-${d.period.replace(/[^a-zA-Z0-9]/g, "-")}`)
          .transition()
          .duration(200)
          .style("opacity", 0);
      })
      // on mouse down, create detailed bar chart
      .on("mousedown", (event, d) => {
        svg.selectAll(".detail-bars").remove();
        const detailData = getDetailData(d); // fetch from data2D
        // console.log(window.innerWidth - width);
        const detailX = d3
          .scaleLinear()
          .range([0, (window.innerWidth - width) / 5.5]) //define width
          .domain([0, d3.max(detailData, (d) => d.count)]);
        const detailY = d3
          .scaleBand()
          .range([0, (y.bandwidth() * detailData.length) / 2])
          .domain(detailData.map((d) => d.subCategory))
          .padding(0.3);
        const detailGroup = svg
          .append("g")
          .attr("class", "detail-bars")
          .attr(
            "transform",
            `translate(${width + 1},  ${y(d.period) - y.bandwidth()})`
          );

        // Append sub-bars
        detailGroup
          .selectAll("rect")
          .data(detailData)
          .join("rect")
          .attr("y", (d) => detailY(d.subCategory))
          .attr("x", 0)
          .attr("height", detailY.bandwidth())
          .attr("width", (d) => detailX(d.count))
          .attr("fill", "#f8db01")
          .style("opacity", 0)
          .transition()
          .duration(300)
          .style("opacity", 1);

        // Append sub-bar labels
        detailGroup
          .selectAll("text")
          .data(detailData)
          .join("text")
          .attr("x", (d) => detailX(d.count) + 5)
          .attr("y", (d) => detailY(d.subCategory) + detailY.bandwidth() / 2)
          .attr("dy", "0.35em")
          .text((d) => `${d.subCategory}: ${d3.format(",")(d.count)}`)
          .style("font-size", "11px")
          .style("fill", "#5c0c06")
          .style("opacity", 0)
          .transition()
          .duration(300)
          .style("opacity", 1);

        //change displayed text
        console.log(detailData);
        d3.select(`#organ-count`).text(d3.format(",")(detailData[0].count));
        d3.select(`#organ-name`).text(detailData[0].subCategory.toLowerCase());
      });

    // add value labels
    svg
      .selectAll(".value-label")
      .data(data)
      .join("text")
      .attr(
        "class",
        (d) => `value-label label-${d.period.replace(/[^a-zA-Z0-9]/g, "-")}`
      )
      .attr("x", (d) => x(d.count) + 5)
      .attr("y", (d) => y(d.period) + y.bandwidth() / 2)
      .attr("dy", "0.35em")
      .text((d) => d3.format(",")(d.count))
      .style("font-size", "11px")
      .style("fill", "#f8db01")
      .style("opacity", 0)
      .on("mouseover", (event, d) => {
        d3.select(`.label-${d.period.replace(/[^a-zA-Z0-9]/g, "-")}`)
          .transition()
          .duration(200)
          .style("opacity", 1);
      });

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
      .style("font-size", "11px")
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
      <!-- Linear Gradient -->
      <linearGradient id="gradient">
        <stop offset="0%" stop-color="#5c0c06" />
        <stop offset="100%" stop-color="#B3353509" />
      </linearGradient>

      <linearGradient id="highlight">
        <stop offset="0%" stop-color="#0D0106" />
        <stop offset="100%" stop-color="#D3311809" />
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
          values="2"
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

  .bg-gradient {
    position: absolute;
    top: 100vh;
    height: 120vh;
    z-index: -1;
  }
</style>
