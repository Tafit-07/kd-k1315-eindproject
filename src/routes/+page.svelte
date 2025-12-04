<script lang="ts">
  import { onMount } from "svelte";
  let down: any;
  let up: any;
  let whoosh: any;
  let modB: string = "";
  let imgB: string = "translate-y-[2px] z-10";
  let hide: boolean = false;
  let hover: any;
  let arr: any[] = [];
  let group: any[] = [
    0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18,
  ];
  let period: any[] = [0, 1, 2, 3, 4, 5, 6, 7, "", 6, 7];
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
        element.xpos = groupNum;
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
    if (window.innerWidth < 1600 || window.innerHeight < 830) {
      hide = true;
    } else {
      hide = false;
    }
    fetchTable();
    window.addEventListener("resize", () => {
      if (window.innerWidth < 1600 || window.innerHeight < 830) {
        hide = true;
      } else {
        hide = false;
      }
    });
  });
</script>

{#if hide == true}
  <div class="h-dvh w-dvw flex flex-col justify-center items-center">
    <h2 class="text-3xl text-transition-metal font-bold">Screen to small</h2>
    <p>This page needs to be at least 1600px wide and 850px heigh ;(</p>
  </div>
{:else}
  <audio src="./../../static/assets/sound/whoosh.mp3" bind:this={whoosh}
  ></audio>
  <audio src="./../../static/assets/sound/down.mp3" bind:this={down}></audio>
  <audio src="./../../static/assets/sound/up.mp3" bind:this={up}></audio>
  <audio src="./../../static/assets/sound/click.wav" bind:this={hover}></audio>

  <div class="grid grid-cols-12 h-dvh items-center">
    <div
      class="bg-dark-back shadow-lg rounded-r-2xl p-4 border-2 border-l-0 border-light/20 h-[90dvh] overflow-y-scroll"
      style="grid-column: 1/3;"
    >
      {#if selectedElement != undefined}
        <div class="w-full aspect-square">
          <div class="pl-3">
            <button
              class="border-2 relative px-1 border-light/20 border-b-0 bg-back rounded-t-xl active:translate-y-[4px] hover:translate-y-[2px] transition ease-in-out {imgB}"
              on:click={() => {
                tabs = false;
                imgB = "translate-y-[2px] z-10";
                modB = "";
                up.play();
              }}
              on:mousedown={() => {
                down.play();
              }}
              on:mouseenter={() => {
                hover.play();
              }}>Imgage</button
            >
            <button
              class="border-2 relative px-1 border-light/20 border-b-0 bg-back rounded-t-xl active:translate-y-[4px] hover:translate-y-[2px] transition ease-in-out {modB}"
              on:click={() => {
                imgB = "";
                modB = "translate-y-[2px] z-10";
                tabs = true;
                up.play();
              }}
              on:mousedown={() => {
                down.play();
              }}
              on:mouseenter={() => {
                hover.play();
              }}>3D Model</button
            >
          </div>
          <div class="w-full relative h-full">
            {#if tabs === true}
              <model-viewer
                auto-rotate
                auto-rotate-delay="0"
                loading="eager"
                interaction-prompt="none"
                camera-controls
                toneMapping="none"
                class="w-full h-full border-2 border-light/20 rounded-xl bg-back"
                min-camera-orbit="auto auto 70%"
                max-camera-orbit="auto auto 70%"
                alt="Bohr model of {selectedElement.name}"
                src={selectedElement.bohr_model_3d}
              >
                <div slot="progress-bar"></div>
              </model-viewer>
            {:else}
              <img
                class="w-full h-full border-2 border-light/20 rounded-xl object-cover"
                src={selectedElement.image.url}
                alt="image of {selectedElement.name}"
              />
              <div
                class="h-full w-full bg-linear-to-b from-back to-5% to-transperant border-2 border-light/20 rounded-xl absolute top-0"
              ></div>
            {/if}
          </div>
        </div>
        <div class="pt-2">
          <a
            class="py-1 text-2xl font-bold w-fit hover:text-blue-700 text-blue-500 visited:text-purple-500 visited:hover:text-purple-700"
            href={selectedElement.source}
            on:click={() => {
              whoosh.play();
            }}>{selectedElement.name} <i class="nf nf-fa-link"></i></a
          >
          <p class="py-1">{selectedElement.category}</p>
          <p>
            {selectedElement.phase}
            {#if selectedElement.phase === "Solid"}
              <i class="nf nf-fa-circle"></i>
            {:else if selectedElement.phase === "Liquid"}
              <i class="nf nf-md-water"></i>
            {:else if selectedElement.phase === "Gas"}
              <i class="nf nf-md-cloud"></i>
            {/if}
          </p>

          <p class="py-1">{selectedElement.summary}</p>
          <div class="py-1">
            <p>Group: {selectedElement.group}</p>
            <p>Period: {selectedElement.period}</p>
          </div>
          <div class="py-1">
            <p>Discoverd by {selectedElement.discovered_by}</p>
            <p>
              Named by
              {#if selectedElement.named_by != null}
                {selectedElement.named_by}
              {:else}
                unknown
              {/if}
            </p>
          </div>
          <p class="py-1">atomic mass: {selectedElement.atomic_mass}u</p>
          <div class="py-1">
            <p>
              Boiling point: {Math.round(selectedElement.boil - 273.15)}°C | {Math.round(
                selectedElement.boil,
              )}°K <i class="nf nf-fa-temperature_high"></i>
            </p>
            <p>
              Melting point: {Math.round(selectedElement.melt - 273.15)}°C | {Math.round(
                selectedElement.melt,
              )}°K <i class="nf nf-fa-temperature_low"></i>
            </p>
          </div>
        </div>
      {/if}
    </div>
    <div style="grid-column: 3/13;" class="flex justify-center">
      <div class="grid grid-cols-19 w-fit justify-center grid-rows-10 gap-1">
        <div
          class="bg-diatomic-nonmetal bg-polyatomic-nonmetal bg-noble-gas bg-alkali-metal bg-alkaline-earth-metal bg-metalloid bg-transition-metal bg-post-transition-metal bg-lanthanide bg-actinide bg-unknown-probably-transition-metal bg-unknown-probably-post-transition-metal bg-unknown-probably-metalloid bg-unknown-predicted-to-be-noble-gas hidden
          border-diatomic-nonmetal! border-polyatomic-nonmetal! border-noble-gas! border-alkali-metal! border-alkaline-earth-metal! border-metalloid! border-transition-metal! border-post-transition-metal! border-lanthanide! border-actinide! border-unknown-probably-transition-metal! border-unknown-probably-post-transition-metal! border-unknown-probably-metalloid! border-unknown-predicted-to-be-noble-gas!
          text-diatomic-nonmetal! text-polyatomic-nonmetal! text-noble-gas! text-alkali-metal! text-alkaline-earth-metal! text-metalloid! text-transition-metal! text-post-transition-metal! text-lanthanide! text-actinide! text-unknown-probably-transition-metal! text-unknown-probably-post-transition-metal! text-unknown-probably-metalloid! text-unknown-predicted-to-be-noble-gas!
         "
        ></div>
        {#each group as num, i}
          {#if num > 0}
            <div
              class="flex justify-center items-center"
              style="grid-column-start: {i + 1}; grid-row-start: 1;"
            >
              <p>{num}</p>
            </div>
          {/if}
        {/each}
        {#each period as num, i}
          {#if i > 0}
            <div
              class="flex justify-center items-center"
              style="grid-row-start: {i + 1};"
            >
              <p>{num}</p>
            </div>
          {:else}
            <div class="flex flex-col justify-center items-center">
              <p>Group <i class="nf nf-md-arrow_right_thick"></i></p>
              <p>Period <i class="nf nf-md-arrow_down_thick"></i></p>
            </div>
          {/if}
        {/each}
        {#each arr as element}
          <button
            on:mousedown={() => {
              click(element);
              down.play();
            }}
            on:mouseup={() => {
              up.play();
            }}
            on:mouseenter={() => {
              hover.pitch = Math.random() * (100 - 0.1) + 0.1;
              try {
                hover.play();
              } catch (error) {
                console.log(error);
              }
            }}
            id={element.number}
            class="bg-{element.category
              .replaceAll(' ', '-')
              .replaceAll(
                ',',
                '',
              )} shadow-lg hover:cursor-pointer border-2 text-dark-back w-[1fr] rounded-xl aspect-square flex flex-col justify-center relative items-center transition ease-in-out active:scale-100 hover:scale-135 hover:shadow-md hover:text-light! hover:border-light! hover:text-shadow-md hover:text-shadow-light/50 hover:shadow-light/50 hover:z-10"
            style="grid-column: {element.xpos + 1}; grid-row: {element.ypos +
              1};"
          >
            <p class="absolute top-0 left-0 text-md px-1">
              {element.number}
            </p>
            <abbr title={element.name} class="w-fit text-3xl p-2">
              {element.symbol}
            </abbr>
          </button>
        {/each}
        <div
          class="bg-light border-2 border-dark-back rounded-tl-xl rounded-br-xl rounded-tr-[60px] rounded-bl-[60px]"
          style="grid-column-start: 4; grid-row-start: 7; grid-row-end: 12;"
        ></div>
      </div>
    </div>
  </div>
{/if}
