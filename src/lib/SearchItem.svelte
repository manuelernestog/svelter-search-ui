<script>
  import { createEventDispatcher } from "svelte";

  export let title,
    url = "#",
    target = "",
    subtitle = "",
    isRecentItem = false;

  const dispatch = createEventDispatcher();

  function onSelect() {
    dispatch("select");
    saveToLocalStorage();
  }

  function saveToLocalStorage() {
    let recentItems = JSON.parse(localStorage.getItem("sevelter-search-recent-items")) || [];
    const item = { title, url, subtitle, isRecentItem: true };
    recentItems = recentItems.filter((i) => !(i.title === item.title && i.url === item.url));
    recentItems.unshift(item);
    if (recentItems.length > 15) {
      recentItems.pop();
    }
    localStorage.setItem("sevelter-search-recent-items", JSON.stringify(recentItems));
  }
</script>

<li>
  <a
    href={url}
    on:click={onSelect}
    {target}
    class="flex items-center w-full px-4 py-2 rounded-md cursor-pointer select-none group dark:hover:bg-gray-800 hover:bg-gray-100"
    id="option-1"
    role="option"
    tabindex="-1"
  >
    <slot name="media">
      <div class="flex-none w-6 h-6 text-gray-400">
        {#if isRecentItem}
          <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24"
            ><g fill="none" stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5"
              ><circle cx="12" cy="12" r="9" /><path d="M11 8v5h5" /></g
            ></svg
          >
        {:else}
          <svg fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" aria-hidden="true" data-slot="icon">
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              d="M2.25 12.75V12A2.25 2.25 0 0 1 4.5 9.75h15A2.25 2.25 0 0 1 21.75 12v.75m-8.69-6.44-2.12-2.12a1.5 1.5 0 0 0-1.061-.44H4.5A2.25 2.25 0 0 0 2.25 6v12a2.25 2.25 0 0 0 2.25 2.25h15A2.25 2.25 0 0 0 21.75 18V9a2.25 2.25 0 0 0-2.25-2.25h-5.379a1.5 1.5 0 0 1-1.06-.44Z"
            />
          </svg>
        {/if}
      </div>
    </slot>

    <div class="flex flex-col">
      <span class="flex-auto ml-3 truncate">{title}</span>
      {#if subtitle}
        <span class="flex-auto ml-3 text-xs truncate opacity-75">{subtitle}</span>
      {/if}
    </div>
    <div class="grow"></div>

    <div class="hidden group-hover:block">
      <svg xmlns="http://www.w3.org/2000/svg" class="w-5 h-5" viewBox="0 0 24 24"
        ><path fill="currentColor" d="M10 6L8.59 7.41L13.17 12l-4.58 4.59L10 18l6-6z" /></svg
      >
    </div>
  </a>
</li>
