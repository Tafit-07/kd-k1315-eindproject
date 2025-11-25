<script lang="ts">
  import { onMount } from "svelte";
  let arr: any[] = [];

  async function fetchTable() {
    let call = await fetch("http://localhost:3000/elements");
    let res = await call.json();
    arr = res;
    for (let i = 0; i < res.length; i++) {
      const element = res[i];
      console.log(element);
    }
  }

  onMount(() => {
    fetchTable();
  });
</script>

{#each arr as element}
  <p>
    {element.number} - {element.symbol} group: {element.group} period:
    {element.period}
  </p>
{/each}
