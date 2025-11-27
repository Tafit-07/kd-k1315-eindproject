<script lang="ts">
  import { onMount } from "svelte";
  let arr: any[] = [];
  let selectedElement: any;
  let tabs = false;
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
  }

  let selectedEl: any;
  let pastEl: any;

  let click = (el: any) => {
    selectedElement = el;
    selectedEl?.classList.remove(
      `border-${pastEl.category.replaceAll(" ", "-").replaceAll(",", "")}!`,
    );
    selectedEl?.classList.remove(
      `text-${pastEl.category.replaceAll(" ", "-").replaceAll(",", "")}!`,
    );
    selectedEl?.classList.remove("bg-white!");

    selectedEl = document.getElementById(el.number);
    pastEl = el;

    selectedEl.classList.add(
      `border-${pastEl.category.replaceAll(" ", "-").replaceAll(",", "")}!`,
    );
    selectedEl.classList.add(
      `text-${pastEl.category.replaceAll(" ", "-").replaceAll(",", "")}!`,
    );
    selectedEl.classList.add("bg-white!");
  };

  onMount(() => {
    fetchTable();
  });
</script>

<div class="grid grid-cols-12 gap-4 h-dvh items-center">
  <div class="bg-slate-900 h-dvh" style="grid-column: 1/3;">
    {#if selectedElement != undefined}
      <div class="w-full aspect-square">
        <div>
          <button
            on:click={() => {
              tabs = false;
            }}>imgage</button
          >
          <button
            on:click={() => {
              tabs = true;
            }}>model</button
          >
        </div>
        {#if tabs === true}
          <model-viewer
            class="w-full h-full"
            camera-controls
            min-camera-orbit="auto auto 70%"
            max-camera-orbit="auto auto 70%"
            alt="Bohr model of {selectedElement.name}"
            src={selectedElement.bohr_model_3d}
          >
          </model-viewer>
        {:else}
          <img
            class="w-full h-full object-cover"
            src={selectedElement.image.url}
            alt="image of {selectedElement.name}"
          />
        {/if}
      </div>
      <h2>{selectedElement.name}</h2>
      <p>category: {selectedElement.category}</p>
      <p>boil: {selectedElement.boil - 273.15}</p>
      <p>melt: {selectedElement.melt - 273.15}</p>
    {/if}
  </div>
  <div style="grid-column: 3/13;" class="flex justify-center">
    <div class="grid grid-cols-18 w-fit justify-center grid-rows-10 gap-1">
      <div
        class="bg-diatomic-nonmetal! bg-polyatomic-nonmetal! bg-noble-gas! bg-alkali-metal! bg-alkaline-earth-metal! bg-metalloid! bg-transition-metal! bg-post-transition-metal! bg-lanthanide! bg-actinide! bg-unknown hidden
          border-diatomic-nonmetal! border-polyatomic-nonmetal! border-noble-gas! border-alkali-metal! border-alkaline-earth-metal! border-metalloid! border-transition-metal! border-post-transition-metal! border-lanthanide! border-actinide!
          text-diatomic-nonmetal! text-polyatomic-nonmetal! text-noble-gas text-alkali-metal! text-alkaline-earth-metal! text-metalloid! text-transition-metal! text-post-transition-metal! text-lanthanide! text-actinide!
         "
      ></div>
      {#each arr as element}
        <button
          on:click={() => {
            click(element);
          }}
          id={element.number}
          class="bg-{element.category
            .replaceAll(' ', '-')
            .replaceAll(
              ',',
              '',
            )} border-2 w-[1fr] rounded-xl aspect-square flex flex-col justify-center relative items-center transition ease-in-out hover:scale-135 hover:shadow-md hover:text-slate-50! hover:border-slate-50! hover:text-shadow-md hover:text-shadow-slate-50/50 hover:shadow-slate-50/50 hover:z-10 hover:bg-blue-600!"
          style="grid-column: {element.group}; grid-row: {element.period};"
        >
          <p class="absolute top-0 left-0 px-1">
            {element.number}
          </p>
          <abbr title={element.name} class="w-fit text-3xl p-2">
            {element.symbol}
          </abbr>
        </button>
      {/each}
      <div
        class="bg-white rounded-tl-xl rounded-br-xl rounded-tr-[60px] rounded-bl-[60px]"
        style="grid-row-start: 6; grid-row-end: 11;"
      ></div>
    </div>
  </div>
</div>
