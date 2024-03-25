<script>
  import { onMount, getContext } from "svelte";

  const { getMap } = getContext("map");
  const map = getMap();

  export let displayAreaLevel;

  let scoreLayer = "Hide";
  let PMTILES_URL =
    "https://storage.googleapis.com/very-nice-tiles-bucket/oa_scores.pmtiles";
  const source = "oa_scores";
  const layer = "oa_scores";

  onMount(async () => {
    map.addSource("oa_scores", {
      type: "vector",
      url: "pmtiles://" + PMTILES_URL,
    });
    setLayer();
  });

  function setLayer() {
    console.log(scoreLayer);
    if (map.getLayer(layer)) {
      map.removeLayer(layer);
    }
    if (scoreLayer != "Hide") {
      map.addLayer({
        id: layer,
        source: source,
        "source-layer": "oa_scores",
        type: "fill",
        paint: {
          "fill-color": [
            "interpolate",
            ["linear"],
            ["get", scoreLayer],
            10,
            "#67001f",
            20,
            "#b2182b",
            30,
            "#d6604d",
            40,
            "#f4a582",
            50,
            "#fddbc7",
            60,
            "#d1e5f0",
            70,
            "#92c5de",
            80,
            "#4393c3",
            90,
            "#2166ac",
            100,
            "#053061",
          ],
          // "fill-outline-color": "rgba(0, 0, 0, 0.2)",
          "fill-opacity": 0.7,
        },
      });
    }
  }

  function scoreTypes() {
    let purposes = [
      "Business",
      "Education",
      "Shopping",
      "Residential",
      "Entertainment",
    ];
    let modes = ["Driving", "Walking", "Cycling", "PT"];
    let all = ["Hide", "Overall"];

    for (let mode of modes) {
      all.push(`Overall ${mode}`);
    }

    for (let purpose of purposes) {
      all.push(`Overall ${purpose}`);
      for (let mode of modes) {
        all.push(`${purpose} by ${mode}`);
      }
    }
    return all;
  }

  function resetScoreLayer() {
    scoreLayer = "Hide";
    setLayer();
  }
</script>

{#if displayAreaLevel == "OA"}
  <div class="govuk-form-group" style="display: flex;">
    <label
      class="govuk-label"
      for="scoreLayer"
      style="margin-right: 10px; margin-top: 8px; font-size: 1rem;"
    >
      OA scores:
    </label>
    <select
      class="govuk-select"
      id="scoreLayer"
      name="scoreLayer"
      bind:value={scoreLayer}
      on:change={setLayer}
    >
      {#each scoreTypes() as x}
        <option value={x}>{x}</option>
      {/each}
    </select>
  </div>
{:else}
  {resetScoreLayer()}
{/if}

<style>
  div {
    z-index: 1;
    position: absolute;
    top: 30px;
    left: 10px;
    background: white;
    padding: 5px;
    border-radius: 10px;
    box-shadow: 2px 3px 3px rgba(0, 0, 0, 0.2);
  }

  select {
    font-size: 16px;
    padding: 4px 8px;
  }
</style>
