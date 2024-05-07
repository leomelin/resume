<script lang="ts">
    import "../app.css";

    import _ from 'lodash';
    import Image from "$lib/Image.svelte";
    import type { PageData } from './$types'

    export let data: PageData
    type TechItemsType = PageData['techExperience'];
    let techItemSelected: undefined | (TechItemsType[0] & { isVisible: boolean });
    export let filterText: string = '';

    export const getTechItems = (f: string): (TechItemsType[0] & { isVisible: boolean })[] => {
        const items =  _.sortBy(data.techExperience, ['title']);
        return items.map(i => {
            return {
                ...i,
                isVisible: !f.trim() || i.title.toLowerCase().includes(f.toLowerCase())
            }
        });
    }
</script>

<style lang="postcss">
    :global(html) {
        background-color: theme(colors.gray.950);
        background-image: url("/background2.jpg");
        font-family: "Roboto", sans-serif;
        font-weight: 400;
        font-style: normal;
        overflow-x: hidden;
    }

</style>

<div class="flex justify-center">
    <div class="flex flex-col lg:flex-row bg-zinc-900 m-4 lg:p-8 lg:m-8 max-w-6xl">
        <div class="lg:flex-1 relative">
            <Image data="{data}" />
        </div>
        <div class="text-white mt-8 lg:mt-0 lg:w-1/2 pl-8 pr-8 pb-8">
            <h1>{data.title}</h1>
            <div class="flex mt-1">
            {#each data.subtitles as titleItem, index}
                <div class="text-cyan-400 text-sm">{titleItem}</div>
                {#if index !== data.subtitles.length - 1}
                    <div class="subtitle-divider text-gray-300 pl-2 pr-2">/</div>
                {/if}
            {/each}
            </div>
            {#each data.introText as introTextP}
                <p class="mt-8 text-justify">
                    {introTextP}
                </p>
            {/each}

        </div>
    </div>
</div>


<div class="flex justify-center">
    <div class="flex flex-col lg:flex-row bg-zinc-900 m-4 lg:p-8 lg:m-8 max-w-6xl">
        <div class="main-block lg:mt-0 p-8">
            <h1>Work experience</h1>

            <div class="flex mt-4 flex-col gap-8">
                {#each data.workExperience as experienceItem}
                    <div class="flex flex-col lg:flex-row gap-8 lg:gap-16">
                        <div class="flex flex-col text-cyan-400 min-w-40">
                            <div class="flex gap-2 text-xl font-bold pt-2">
                                <div class="flex">{experienceItem.timeStart}</div><div class="text-gray-300">-</div><div>{experienceItem.timeEnd}</div>
                            </div>
                            <div class="text-white text-base font-bold pt-2">{experienceItem.company}</div>
                            <div class="text-cyan-400 text-sm">{experienceItem.title}</div>

                        </div>
                        <div class="text-white italic text-justify">{experienceItem.description}</div>
                    </div>
                {/each}
            </div>

        </div>
    </div>
</div>

<div class="flex justify-center">
    <div class="flex flex-col lg:flex-row bg-zinc-900 m-4 lg:p-8 lg:m-8 max-w-6xl w-full">
        <div class="main-block lg:mt-0 p-8 flex-1">
            <h1 class="mb-8">Tech experience</h1>
            <div class="flex mt-4 flex-col gap-8 text-white">
                <div class="relative">
                    <img src="/funnel.svg" alt="icon for filtering" class="w-6 h-6 absolute top-2 left-2" />
                    <div class="text-cyan-400 font-bold border-dotted border-zinc-500 border-2 text-center p-8 mt-8 rounded min-h-32 flex flex-col items-center justify-center">
                        {#if !techItemSelected}
                            <p class="italic select-none font-light">Hover or click the item to reveal some info...</p>
                        {/if}
                        {#if techItemSelected}
                            <h2 class="text-white font-bold">{techItemSelected.title}</h2>
                            <p>{techItemSelected.description}</p>
                        {/if}
                    </div>
                    <input bind:value={filterText} type="text" placeholder="filter..." class="rounded p-2 pl-10 text-white w-full bg-zinc-600 mt-8">
                    <div class="flex gap-6 gap-y-0 justify-center mt-8 flex-wrap">
                        {#each getTechItems(filterText) as techItem}
                            <button on:click={() => { techItemSelected = techItem; }}
                                    on:mouseover={() => { techItemSelected = techItem; }}
                                    on:focus={() => () => { techItemSelected = techItem; }}
                                    on:mouseout={() => { techItemSelected = undefined; }}
                                    on:blur={() => { techItemSelected = undefined; }}
                                    class="font-bold hover:scale-125 hover:text-cyan-400 transform transition duration-300 cursor-pointer flex items-center"
                                    style="font-size: {(techItem.level * 5 + 5)/17}rem; opacity: {20*techItem.level/100};visibility:{techItem?.isVisible ? 'visible': 'hidden'}">{techItem.title}</button>
                        {/each}
                    </div>
                </div>
            </div>

        </div>
    </div>
</div>
