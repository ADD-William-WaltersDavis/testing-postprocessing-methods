<script>
  import "govuk-frontend/govuk/all.scss";

  import Map from "./components/Map.svelte";
  import HoverLayer from "./components/HoverLayer.svelte";
  import BaseScoreLayerOApmtile from "./components/BaseScoreLayerOApmtile.svelte";

  let hoveredAreaScores;
  let responseJson;
  let hoveredInterventionScores;
  let leftSidebarClassToggle;
  let rightSidebarClassToggle;
  let stopLayerToggle;
  let displayAreaLevel = "OA";
  let loading;
  export let innerWidth = 0;
  export let innerHeight = 0;
  export let login_username = "user";
</script>

<svelte:window bind:innerWidth bind:innerHeight />

<div>
  <Map {innerHeight}>

    <HoverLayer />

    <!-- <BaseScoreLayerOA
      bind:hoveredAreaScores
      {rightSidebarClassToggle}
      {displayAreaLevel}
      {responseJson}
    /> -->
    <BaseScoreLayerOApmtile 
      bind:hoveredAreaScores
      {rightSidebarClassToggle}
      {displayAreaLevel}
      {responseJson}
    />
  </Map>
</div>
