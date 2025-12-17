<script lang="ts">
  import "../app.css";
  import favicon from "$lib/assets/favicon.svg";

  let { children } = $props();
</script>

<svelte:head>
  <link rel="icon" href={"./../../static/assets/img/icon.svg"} />
  <title>Periodic Table</title>
  <script
    type="module"
    src="https://unpkg.com/@google/model-viewer/dist/model-viewer.min.js"
  ></script>
</svelte:head>

<div class="text-white font-raleway bg-back">
  {@render children()}
</div>
