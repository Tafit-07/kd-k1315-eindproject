<script lang="ts">
  import { onMount } from "svelte";
  let arr: Any = [];

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

<h1>Welcome to SvelteKit</h1>
<p>
  Visit <a href="https://svelte.dev/docs/kit">svelte.dev/docs/kit</a> to read the
  documentation
</p>
{#each arr as element, i}
  <p>
    {element.number} - {element.name} group:{element.group} period:{element.period}
  </p>
{/each}
