<script>
  import fileNames from "../data/file_names.json";
  import { onMount, getContext } from "svelte";

  const { getMap } = getContext("map");
  const map = getMap();

  export let displayAreaLevel;

  let scoreLayer = "Hide";
  let sourceLayer = fileNames[0];
  const layer = "oa_scores";

  onMount(async () => {
    for (let i = 0; i < fileNames.length; i++) {
      map.addSource(fileNames[i], {
        type: "vector",
        url: "pmtiles://" + "https://storage.googleapis.com/very-nice-tiles-bucket/testing-postprocessing-methods/" + fileNames[i] + ".pmtiles",
      }); 
    } 
    // setLayer();
  });
  let scaling_factors;
  // hover over score change
  let geo;
  let hoverScore;
  map.on("mousemove", layer, function (e) {
    if (e.features.length > 0) {
      if (sourceLayer.includes("lsoa")) {
        geo = e.features[0].properties["LSOA11CD"];
      } else {
        geo = e.features[0].properties["oa11cd"];
      }
      hoverScore = e.features[0].properties[scoreLayer];

      // console.log(geo);
      // console.log(hoverScore);
    }
  });
  map.on("mouseleave", layer, function () {
    geo = null;
    hoverScore = null;
  });

  function setLayer() {
    console.log(scoreLayer);
    if (map.getLayer(layer)) {
      map.removeLayer(layer);
    }
    if (scoreLayer != "Hide") {
      if (custom_min && custom_max) {

      } else {
        if (sourceLayer.includes("diff")){
          scaling_factors = [-40, -30, -20, -10, 0, 10, 20, 30, 40, 50];
        } else {
          scaling_factors = [10, 20, 30, 40, 50, 60, 70, 80, 90, 100];
        }
      } 
      console.log("scaling_factors")
      console.log(scaling_factors)
      map.addLayer({
        id: layer,
        source: sourceLayer,
        "source-layer": "temp",
        type: "fill",
        paint: {
          "fill-color": [
            "interpolate",
            ["linear"],
            ["get", scoreLayer],
            scaling_factors[0],
            "#67001f",
            scaling_factors[1],
            "#b2182b",
            scaling_factors[2],
            "#d6604d",
            scaling_factors[3],
            "#f4a582",
            scaling_factors[4],
            "#fddbc7",
            scaling_factors[5],
            "#d1e5f0",
            scaling_factors[6],
            "#92c5de",
            scaling_factors[7],
            "#4393c3",
            scaling_factors[8],
            "#2166ac",
            scaling_factors[9],
            "#053061",
          ],
          // "fill-outline-color": "rgba(0, 0, 0, 0.2)",
          "fill-opacity": 0.7,
        },
      });
    }
  }

  function scoreTypes() {
    return ['Hide',
            'oa11cd',
            'LSOA11CD',
            'Business_car',
            'Education_car',
            'Health_car',
            'Entertainment_car',
            'Shopping_car',
            'Residential_car',
            'Business_walk',
            'Education_walk',
            'Health_walk',
            'Entertainment_walk',
            'Shopping_walk',
            'Residential_walk',
            'Business_pt',
            'Education_pt',
            'Health_pt',
            'Entertainment_pt',
            'Shopping_pt',
            'Residential_pt',
            'Business_cycling',
            'Education_cycling',
            'Health_cycling',
            'Entertainment_cycling',
            'Shopping_cycling',
            'Residential_cycling',
            'Overall_pt',
            'Overall_cycling',
            'Overall_car',
            'Overall_walk',
            'Overall_Business',
            'Overall_Education',
            'Overall_Health',
            'Overall_Entertainment',
            'Overall_Shopping',
            'Overall_Residential',
            'Overall_Overall']
  }
  function fileSources() {
    return fileNames;
  }

  // function scoreTypes() {
  //   let purposes = [
  //     "Business",
  //     "Education",
  //     "Shopping",
  //     "Residential",
  //     "Entertainment",
  //   ];
  //   let modes = ["Driving", "Walking", "Cycling", "PT"];
  //   let all = ["Hide", "Overall"];

  //   for (let mode of modes) {
  //     all.push(`Overall ${mode}`);
  //   }

  //   for (let purpose of purposes) {
  //     all.push(`Overall ${purpose}`);
  //     for (let mode of modes) {
  //       all.push(`${purpose} by ${mode}`);
  //     }
  //   }
  //   return all;
  // }

  function resetScoreLayer() {
    scoreLayer = "Hide";
    setLayer();
  }
  let custom_min = null;
  let custom_max = null;

  function reset_custom_scores() {
    custom_min = null;
    custom_max = null;
    setLayer();
  }
  function apply_custom_range() {
    

    if (custom_min && custom_max) {
      if (custom_max < custom_min) {
        window.alert(`max less than min`);
      }
      let max_to_min = custom_max - custom_min
      scaling_factors = [];
      for (let i = 1; i <= 10; i++) {
        let v = custom_min + (max_to_min/10)*i
        scaling_factors.push(v)
      } 
    }

    setLayer()
  }
</script>

{#if displayAreaLevel == "OA"}
  <div class="govuk-form-group" style="display: flex; top: 30px; left: 10px;">
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
  <div class="govuk-form-group" style="display: flex; top: 90px; left: 10px;">
    <label
      class="govuk-label"
      for="scoreLayer"
      style="margin-right: 10px; margin-top: 8px; font-size: 1rem;"
    >
      Source:
    </label>
    <select
      class="govuk-select"
      id="sourceLayer"
      name="sourceLayer"
      bind:value={sourceLayer}
      on:change={setLayer}
    >
      {#each fileSources() as x}
        <option value={x}>{x}</option>
      {/each}
    </select>
  </div>
{:else}
  {resetScoreLayer()}
{/if}

<div style="display: flex; top: 30px; right: 10px; font-size: 1rem; padding: 20px;">
  OA/LSOA: {geo}
  <br/>
  {scoreLayer}: {hoverScore}

</div>

<div style="top: 150px; left: 10px; font-size: 1rem; padding: 20px;">
  Set custom range:
  <br/>
  <br/>
  <label for="min-input">Min:</label>
  <input
    id="min-input"
    type="number"
    title="Min"
    style="float: right; font-size: 0.8rem;"
    bind:value={custom_min}
  />
  <br/>
  <br/>
  <label for="max-input">Max:</label>
  <input
    id="max-input"
    type="number"
    title="Max"
    style="float: right; font-size: 0.8rem;"
    bind:value={custom_max}
  />
  <br/>
  <br/>
  <button 
    id="resetButton"
    on:click={apply_custom_range}
    >
    Apply custom range
  </button>
  <br/>
  <br/>
  <button 
    id="resetButton"
    on:click={reset_custom_scores}
    >
    Reset range to default
  </button>

</div>


<style>
  div {
    z-index: 1;
    position: absolute;
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
