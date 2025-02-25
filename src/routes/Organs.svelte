<script>
  let container;

  let organs = [
    { name: "kidney", number: 0, dimension: "300px" },
    { name: "liver", number: 0, dimension: "400px" },
    { name: "heart", number: 0, dimension: "100px" },
    { name: "lungs", number: 0, dimension: "150px" },
    { name: "pancreas", number: 0, dimension: "100px" },
    { name: "intestine", number: 0, dimension: "100px" },
  ];
</script>

<section bind:this={container} class="grid-container">
  {#each organs as src}
    <div class="grid-item" style="width: {src.dimension};">
      <img
        class="illustration"
        src={"/anatomy/" + src.name + ".png"}
        alt={src.name}
        width="100%"
      />
      <!-- <p>{src.name}</p> -->
    </div>
  {/each}
</section>

<style>
  .grid-container {
    display: flex;
    flex-wrap: nowrap;
    gap: clamp(10px, 5vw, 80px); /* Dynamically changes gap */
    justify-content: center;
    align-items: center;
    padding: 20px;
    margin-top: 120px;
  }

  .grid-item {
    display: flex;
    flex-direction: column;
    align-items: center;
    text-align: center;
  }

  .illustration {
    margin-bottom: 10px;
  }
</style>
