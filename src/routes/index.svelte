<script>
    import { fly } from "svelte/transition";
    import { page } from "$app/stores";

    import Search from "$lib/components/Search.svelte";

    let query = sanitizeQuery($page.url.searchParams.get("query")) || "";

    let header;
    let content = {};
    let showHeader = true;
    let headerStateTimeout;

    let offset = 48;
    let tolerance = 48;

    let scrollY = 0;
    let lastScrollY = 0;

    function sanitizeQuery(query) {
        if (!query) return;

        return query
                .replace("_", " ")
                .trim();
    }

    function handleScroll() {
        clearTimeout(headerStateTimeout);
        headerStateTimeout = setTimeout(() => {
            showHeader = getHeaderState(scrollY);
        }, 30)
    }

    function getHeaderState(scrollY) {
        const dy = lastScrollY - scrollY;
        lastScrollY = scrollY;

        if (scrollY < offset) {
            return true;
        }

        if (Math.abs(dy) <= tolerance)
            return showHeader;

        if (dy > 0) {
            return true;
        }

        return false;
    };

    const snippet = $page.url.searchParams.get("snippet");
</script>

<svelte:window 
    on:scroll={handleScroll}
    bind:scrollY
/>

<svelte:head>
    {#if content.hasOwnProperty("reference")}
        <title>The Bible | {content.reference}</title>
    {:else}
        <title>The Bible</title>
    {/if}
</svelte:head>

<div 
    in:fly={{ y:100 }}
    out:fly={{ y:100 }}
    class="sticky top-0 w-full border-gray-400 border-b h-12 bg-white"
    class:disappear={!showHeader}
    class:reappear={showHeader}
    bind:this={header}
>
    <Search 
        bind:query
        bind:response={content}
    />
</div>

<div class="content">
    {#if content.verses}
        {#each content.verses as verse}
            <span class="text-sm align-top">{verse.verse}</span>
            <span class="text-lg">{verse.text}</span>
        {/each}
    {/if}
</div>

<style>
    .disappear {
        transform: translateY(-48px);

        transition: 400ms;
    }

    .reappear {
        transform: translateY(0px);

        transition: 400ms;
    }
</style>