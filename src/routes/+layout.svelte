<script lang="ts">
  import "../app.css";
  import favicon from "$lib/assets/favicon.svg";

  let { children } = $props();
</script>

<svelte:head>
  <link rel="icon" href={"./../../static/assets/img/icon.svg"} />
  <title>Periodic Table</title>
</svelte:head>

<div class="text-white font-raleway bg-back">
  {@render children()}
</div>
