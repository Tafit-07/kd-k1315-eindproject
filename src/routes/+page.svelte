<script lang="ts">
  import { onMount } from "svelte";
  let arr: any[] = [];
  async function fetchTable() {
    let call = await fetch("http://localhost:3000/elements");
    let res = await call.json();
    arr = res;
    let groupNum = 4;
    for (let i = 0; i < arr.length; i++) {
      const element = arr[i];
      if ((i > 55 && i < 71) || (i > 87 && i < 103)) {
        element.period += 3;
        element.group = groupNum;
        if (groupNum < 18) {
          groupNum += 1;
        } else {
          groupNum = 4;
        }
      }
    }
    console.log(arr);
  }

  let click = (el: any) => {
    console.log(el);
  };

  onMount(() => {
    fetchTable();
  });
</script>

<div class="grid grid-cols-18 grid-rows-10 w-fit h-fit">
  <div
    class="bg-diatomic bg-polyatom bg-noble-ga bg-alkali-m bg-alkaline bg-metalloi bg-post-tra bg-transiti bg-lanthani bg-actinide bg-unknown hidden"
  ></div>
  {#each arr as element}
    <button
      on:click={() => {
        click(element);
      }}
      id={element.number}
      class="bg-{element.category
        .replaceAll(' ', '-')
        .slice(0, 8)
        .replaceAll(
          ',',
          '',
        )} border-2 w-20 h-20 flex flex-col justify-center relative items-center transition ease-in-out hover:bg-yellow-600"
      style="grid-column: {element.group}; grid-row: {element.period};"
    >
      <p class="absolute top-0 left-0 px-1">
        {element.number}
      </p>
      <abbr title={element.name} class="w-fit text-3xl">
        {element.symbol}
      </abbr>
    </button>
  {/each}
  <div class="bg-white" style="grid-row-start: 6; grid-row-end: 11;"></div>
</div>
