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
    selectedEl?.classList.remove("bg-dark-back!");

    selectedEl = document.getElementById(el.number);
    pastEl = el;

    selectedEl.classList.add(
      `border-${pastEl.category.replaceAll(" ", "-").replaceAll(",", "")}!`,
    );
    selectedEl.classList.add(
      `text-${pastEl.category.replaceAll(" ", "-").replaceAll(",", "")}!`,
    );
    selectedEl.classList.add("bg-dark-back!");
  };

  onMount(() => {
    fetchTable();
  });
</script>

<div class="grid grid-cols-12 gap-4 h-dvh items-center">
  <div class="bg-dark-back h-dvh" style="grid-column: 1/3;">
    {#if selectedElement != undefined}
      <div class="w-full aspect-square">
        <div>
          <button
            class="border-2 border-b-0"
            on:click={() => {
              tabs = false;
            }}>imgage</button
          >
          <button
            class="border-2 border-b-0"
            on:click={() => {
              tabs = true;
            }}>model</button
          >
        </div>
        <div class="border-2 w-full h-full">
          {#if tabs === true}
            <model-viewer
              class="w-full h-full bg-black"
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
      </div>
      <div>
        <h2 class="py-1 text-xl font-bold">{selectedElement.name}</h2>
        <p class="py-1">category: {selectedElement.category}</p>
        <p class="py-1">{selectedElement.summary}</p>
        <p>
          Boil: {Math.round(selectedElement.boil - 273.15)}°C | {Math.round(
            selectedElement.boil,
          )}°K
        </p>
        <p>
          Melt: {Math.round(selectedElement.melt - 273.15)}°C | {Math.round(
            selectedElement.melt,
          )}°K
        </p>
      </div>
    {/if}
  </div>
  <div style="grid-column: 3/13;" class="flex justify-center">
    <div class="grid grid-cols-18 w-fit justify-center grid-rows-10 gap-1">
      <div
        class="bg-diatomic-nonmetal bg-polyatomic-nonmetal bg-noble-gas bg-alkali-metal bg-alkaline-earth-metal bg-metalloid bg-transition-metal bg-post-transition-metal bg-lanthanide bg-actinide bg-unknown-probably-transition-metal bg-unknown-probably-post-transition-metal bg-unknown-probably-metalloid bg-unknown-predicted-to-be-noble-gas hidden
          border-diatomic-nonmetal! border-polyatomic-nonmetal! border-noble-gas! border-alkali-metal! border-alkaline-earth-metal! border-metalloid! border-transition-metal! border-post-transition-metal! border-lanthanide! border-actinide! border-unknown-probably-transition-metal! border-unknown-probably-post-transition-metal! border-unknown-probably-metalloid! border-unknown-predicted-to-be-noble-gas!
          text-diatomic-nonmetal! text-polyatomic-nonmetal! text-noble-gas text-alkali-metal! text-alkaline-earth-metal! text-metalloid! text-transition-metal! text-post-transition-metal! text-lanthanide! text-actinide! text-unknown-probably-transition-metal! text-unknown-probably-post-transition-metal! text-unknown-probably-metalloid! text-unknown-predicted-to-be-noble-gas!
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
            )} border-2 text-dark-back w-[1fr] rounded-xl aspect-square flex flex-col justify-center relative items-center transition ease-in-out hover:scale-135 hover:shadow-md hover:text-light! hover:border-light! hover:text-shadow-md hover:text-shadow-light/50 hover:shadow-light/50 hover:z-10"
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
        class="bg-light border-2 border-dark-back rounded-tl-xl rounded-br-xl rounded-tr-[60px] rounded-bl-[60px]"
        style="grid-row-start: 6; grid-row-end: 11;"
      ></div>
    </div>
  </div>
</div>
