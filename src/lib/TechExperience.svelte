<script lang="ts">
  import ResumeSection from "$lib/ResumeSection.svelte";
  import type { PageData } from "../../.svelte-kit/types/src/routes/$types.js";
  import _ from "lodash";

  export let filterText: string = "";
  export let data: PageData;

  type TechItemsType = PageData["techExperience"];
  let techItemSelected: undefined | (TechItemsType[0] & { isVisible: boolean });
  let techItemPopupBoxRect: DOMRect | undefined;
  let techItemOffsetTop: number = 0;
  let techItemOffsetLeft: number = 0;

  export const getTechItems = (
    f: string,
  ): (TechItemsType[0] & { isVisible: boolean })[] => {
    const items = _.sortBy(data.techExperience, ["title"]);
    return items.map((i) => {
      return {
        ...i,
        isVisible: !f.trim() || i.title.toLowerCase().includes(f.toLowerCase()),
      };
    });
  };
</script>

<ResumeSection title="Tech experience">
  <div class="relative">
    <div class="relative">
      <img
        src="/funnel.svg"
        alt="icon for filtering"
        class="w-6 h-6 absolute top-2 left-2"
      />
      <input
        bind:value={filterText}
        type="text"
        placeholder="filter..."
        class="rounded p-2 pl-10 text-white w-full bg-zinc-600 mt-8"
      />
    </div>
    <div class="flex gap-6 gap-y-0 justify-center mt-8 flex-wrap relative">
      <div
        class:hidden={!techItemSelected}
        style="bottom: calc({-techItemOffsetTop}px + 100% + 0.5rem)"
        class="absolute bg-zinc-900 bg-opacity-95 z-10 text-cyan-400 font-bold border-dotted border-zinc-500 border-2 text-center p-8 rounded min-h-32 flex flex-col items-center justify-center w-full"
      >
        <h2 class="text-white font-bold">{techItemSelected?.title}</h2>
        <p>{techItemSelected?.description}</p>
        <div
          style="left: calc({techItemOffsetLeft}px - 0.5rem + {(techItemPopupBoxRect?.width ??
            0) / 2}px - 1rem);"
          class="absolute nozzle bg-zinc-900 bg-opacity-95 border-dotted border-zinc-500 border-2 w-4 h-4 rotate-45 border-t-0 border-l-0 -bottom-3 mb-[2px]"
        ></div>
        <button
          class="absolute top-1 left-full w-8 h-8 -ml-9 text-white"
          on:click={() => {
            techItemSelected = undefined;
          }}>x</button
        >
      </div>
      {#each getTechItems(filterText) as techItem}
        <button
          on:click={(e) => {
            if (techItemSelected?.title === techItem.title) {
              techItemSelected = undefined;
            } else {
              const button = e.currentTarget;
              techItemPopupBoxRect = button.getBoundingClientRect();
              techItemOffsetTop = button.offsetTop;
              techItemOffsetLeft = button.offsetLeft;
              techItemSelected = techItem;
            }
          }}
          class="font-bold hover:scale-125 hover:text-cyan-400 transform transition duration-300 cursor-pointer flex items-center"
          style="font-size: {(techItem.level * 5 + 5) / 17}rem; opacity: {(20 *
            techItem.level) /
            100};visibility:{techItem?.isVisible ? 'visible' : 'hidden'}"
          >{techItem.title}</button
        >
      {/each}
    </div>
  </div>
</ResumeSection>
