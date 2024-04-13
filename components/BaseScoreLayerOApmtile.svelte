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

  // hover over score change
  let OA;
  let hoverScore;
  map.on("mousemove", layer, function (e) {
    if (e.features.length > 0) {
      OA = e.features[0].properties["oa11cd"];
      hoverScore = e.features[0].properties[scoreLayer];

      console.log(OA);
      console.log(hoverScore);
    }
  });
  map.on("mouseleave", layer, function () {
    OA = null;
    hoverScore = null;
  });

  function sort_layers() {
    let layers = map.getStyle().layers;
    console.log("layers")
    console.log(layers)
    for (let i = 0; i < layers.length; i++) {
      if (["road_minor", "background"].includes(layers[i]["id"]) ) {
        console.log(layers[i]["id"])
      } else {
        map.removeLayer(layers[i]["id"]);
      }
    } 
    
  }

  function setLayer() {
    console.log(scoreLayer);
    sort_layers()
    // if (map.getLayer(layer)) {
    //   map.removeLayer(layer);
    // }
    // if (scoreLayer != "Hide") {
    //   map.addLayer({
    //     id: layer,
    //     source: sourceLayer,
    //     "source-layer": "temp",
    //     type: "fill",
    //     paint: {
    //       "fill-color": [
    //         "interpolate",
    //         ["linear"],
    //         ["get", scoreLayer],
    //         10,
    //         "#67001f",
    //         20,
    //         "#b2182b",
    //         30,
    //         "#d6604d",
    //         40,
    //         "#f4a582",
    //         50,
    //         "#fddbc7",
    //         60,
    //         "#d1e5f0",
    //         70,
    //         "#92c5de",
    //         80,
    //         "#4393c3",
    //         90,
    //         "#2166ac",
    //         100,
    //         "#053061",
    //       ],
    //       // "fill-outline-color": "rgba(0, 0, 0, 0.2)",
    //       "fill-opacity": 0.7,
    //     },
    //   });
    // }
  }

  function scoreTypes() {
    return ['Hide',
            'oa11cd',
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
  OA: {OA}
  <br/>
  {scoreLayer}: {hoverScore}

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
