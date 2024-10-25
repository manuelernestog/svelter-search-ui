<script>
  import { onMount, createEventDispatcher } from "svelte";
  import SearchGroup from "./SearchGroup.svelte";
  import SearchItem from "./SearchItem.svelte";

  export let show = false,
    placeholder = "Search...",
    resultCount = 0,
    isLoading = false,
    showRecent = true,
    noRecentItemsText = "No recent searches to show",
    recentItemsGroupName = "Recent Searches";

  let isHidden = true;
  let searchInputValue = "";
  let displayClasses = "";
  let isClient = false;
  let searchInput;
  let debounceTimeout;

  let recentSearchItems = [];

  const dispatch = createEventDispatcher();

  $: displayHandler(show);

  onMount(() => {
    isClient = true;
  });

  function handleEscapeKey(event) {
    if (event.key === "Escape") {
      show = false;
    }
  }

  function handleInputChange() {
    clearTimeout(debounceTimeout);
    debounceTimeout = setTimeout(() => {
      if (searchInputValue != "") dispatch("search", { value: searchInputValue });
    }, 350);
  }

  function displayHandler(show) {
    searchInputValue = "";
    if (show) {
      isHidden = false;
      setTimeout(() => {
        searchInput.focus();
        displayClasses = "opacity-100 ease-out duration-300";
        if (isClient) window.addEventListener("keydown", handleEscapeKey);
        if (showRecent) loadRecentItems();
      }, 10);
    } else {
      displayClasses = "opacity-0 ease-in duration-200";
      setTimeout(() => {
        isHidden = true;
        if (isClient) window.removeEventListener("keydown", handleEscapeKey);
      }, 200);
    }
  }

  function clearInput() {
    searchInputValue = "";
    searchInput.focus();
  }

  function loadRecentItems() {
    const items = localStorage.getItem("sevelter-search-recent-items");
    if (items) {
      recentSearchItems = JSON.parse(items);
    }
  }
</script>

<div
  class:hidden={isHidden}
  class={"fixed top-0 w-screen h-screen flex flex-row items-start justify-center z-40 duration-300 ease-out " + displayClasses}
  role="dialog"
  aria-modal="true"
>
  <div
    class="fixed inset-0 w-screen h-screen transition-opacity bg-black bg-opacity-50"
    aria-hidden="true"
    on:click={() => (show = false)}
  ></div>

  <div class="w-full h-full overflow-y-auto sm:p-6 md:p-20">
    <div
      class="h-full mx-auto overflow-hidden transition-all transform bg-white divide-y divide-gray-100 shadow-2xl dark:divide-gray-500 dark:divide-opacity-20 dark:bg-gray-900 sm:max-w-xl sm:h-auto sm:rounded-xl ring-1 ring-black ring-opacity-5"
    >
      <div class="relative">
        {#if !isLoading}
          <svg
            class="hidden sm:block pointer-events-none absolute left-4 top-3.5 h-5 w-5 text-gray-400"
            viewBox="0 0 20 20"
            fill="currentColor"
            aria-hidden="true"
            data-slot="icon"
          >
            <path
              fill-rule="evenodd"
              d="M9 3.5a5.5 5.5 0 1 0 0 11 5.5 5.5 0 0 0 0-11ZM2 9a7 7 0 1 1 12.452 4.391l3.328 3.329a.75.75 0 1 1-1.06 1.06l-3.329-3.328A7 7 0 0 1 2 9Z"
              clip-rule="evenodd"
            />
          </svg>

          <button on:click={() => (show = false)} class="absolute sm:hidden left-4 top-4.5">
            <svg xmlns="http://www.w3.org/2000/svg" class="w-3.5 h-3.5 text-gray-400" viewBox="0 0 15 15" fill="currentcolor"
              ><polygon points="16 7 3.83 7 9.42 1.41 8 0 0 8 8 16 9.41 14.59 3.83 9 16 9"></polygon></svg
            >
          </button>
        {:else}
          <svg
            aria-hidden="true"
            class="absolute w-6 h-6 text-gray-200 top-3 left-3 animate-spin dark:text-gray-600 fill-blue-600"
            viewBox="0 0 100 101"
            fill="none"
            xmlns="http://www.w3.org/2000/svg"
          >
            <path
              d="M100 50.5908C100 78.2051 77.6142 100.591 50 100.591C22.3858 100.591 0 78.2051 0 50.5908C0 22.9766 22.3858 0.59082 50 0.59082C77.6142 0.59082 100 22.9766 100 50.5908ZM9.08144 50.5908C9.08144 73.1895 27.4013 91.5094 50 91.5094C72.5987 91.5094 90.9186 73.1895 90.9186 50.5908C90.9186 27.9921 72.5987 9.67226 50 9.67226C27.4013 9.67226 9.08144 27.9921 9.08144 50.5908Z"
              fill="currentColor"
            />
            <path
              d="M93.9676 39.0409C96.393 38.4038 97.8624 35.9116 97.0079 33.5539C95.2932 28.8227 92.871 24.3692 89.8167 20.348C85.8452 15.1192 80.8826 10.7238 75.2124 7.41289C69.5422 4.10194 63.2754 1.94025 56.7698 1.05124C51.7666 0.367541 46.6976 0.446843 41.7345 1.27873C39.2613 1.69328 37.813 4.19778 38.4501 6.62326C39.0873 9.04874 41.5694 10.4717 44.0505 10.1071C47.8511 9.54855 51.7191 9.52689 55.5402 10.0491C60.8642 10.7766 65.9928 12.5457 70.6331 15.2552C75.2735 17.9648 79.3347 21.5619 82.5849 25.841C84.9175 28.9121 86.7997 32.2913 88.1811 35.8758C89.083 38.2158 91.5421 39.6781 93.9676 39.0409Z"
              fill="currentFill"
            />
          </svg>
        {/if}

        <input
          bind:value={searchInputValue}
          bind:this={searchInput}
          type="text"
          class="w-full h-12 text-gray-900 bg-transparent border-0 dark:text-white pr-11 pl-11 placeholder:text-gray-400 focus:ring-0 sm:text-sm"
          {placeholder}
          role="combobox"
          aria-expanded="false"
          aria-controls="options"
          on:input={handleInputChange}
        />

        <button
          class:hidden={searchInputValue == ""}
          class="absolute text-gray-400 text-md text- top-3.5 right-5"
          on:click={clearInput}
        >
          &#10005;
        </button>
      </div>

      {#if searchInputValue == ""}
        {#if showRecent}
          <slot name="recent-results">
            {#if recentSearchItems.length > 0}
              <ul
                class="p-4 pb-2 space-y-4 overflow-y-auto max-h-[calc(100%-55px)] sm:max-h-96 transform-gpu scroll-py-10 scroll-pb-2"
                id="options"
                role="listbox"
              >
                <SearchGroup name={recentItemsGroupName} resultCount={recentSearchItems.length}>
                  {#each recentSearchItems as item}
                    <SearchItem
                      title={item.title}
                      url={item.url}
                      subtitle={item.subtitle}
                      on:select={() => (show = false)}
                      isRecentItem={true}
                    />
                  {/each}
                </SearchGroup>
              </ul>
            {:else}
              <div class="px-6 text-sm text-center py-14 sm:px-14">
                <slot name="no-recent-results">
                  <p class="mt-4 font-semibold text-gray-900 dark:text-gray-400">{noRecentItemsText}</p>
                </slot>
              </div>
            {/if}
          </slot>
        {:else}
          <div class="px-6 text-sm text-center py-14 sm:px-14">
            <slot name="info">
              <svg
                class="w-6 h-6 mx-auto text-gray-400"
                fill="none"
                viewBox="0 0 24 24"
                stroke-width="1.5"
                stroke="currentColor"
                aria-hidden="true"
                data-slot="icon"
              >
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  d="M16.712 4.33a9.027 9.027 0 0 1 1.652 1.306c.51.51.944 1.064 1.306 1.652M16.712 4.33l-3.448 4.138m3.448-4.138a9.014 9.014 0 0 0-9.424 0M19.67 7.288l-4.138 3.448m4.138-3.448a9.014 9.014 0 0 1 0 9.424m-4.138-5.976a3.736 3.736 0 0 0-.88-1.388 3.737 3.737 0 0 0-1.388-.88m2.268 2.268a3.765 3.765 0 0 1 0 2.528m-2.268-4.796a3.765 3.765 0 0 0-2.528 0m4.796 4.796c-.181.506-.475.982-.88 1.388a3.736 3.736 0 0 1-1.388.88m2.268-2.268 4.138 3.448m0 0a9.027 9.027 0 0 1-1.306 1.652c-.51.51-1.064.944-1.652 1.306m0 0-3.448-4.138m3.448 4.138a9.014 9.014 0 0 1-9.424 0m5.976-4.138a3.765 3.765 0 0 1-2.528 0m0 0a3.736 3.736 0 0 1-1.388-.88 3.737 3.737 0 0 1-.88-1.388m2.268 2.268L7.288 19.67m0 0a9.024 9.024 0 0 1-1.652-1.306 9.027 9.027 0 0 1-1.306-1.652m0 0 4.138-3.448M4.33 16.712a9.014 9.014 0 0 1 0-9.424m4.138 5.976a3.765 3.765 0 0 1 0-2.528m0 0c.181-.506.475-.982.88-1.388a3.736 3.736 0 0 1 1.388-.88m-2.268 2.268L4.33 7.288m6.406 1.18L7.288 4.33m0 0a9.024 9.024 0 0 0-1.652 1.306A9.025 9.025 0 0 0 4.33 7.288"
                />
              </svg>
              <p class="mt-4 font-semibold text-gray-900 dark:text-gray-400">Help with searching</p>
              <p class="mt-2 text-gray-500">
                Use this tool to quickly search for users and projects across our entire platform. You can also use the
                search modifiers found in the footer below to limit the results to just users or projects.
              </p>
            </slot>
          </div>
        {/if}
      {:else if resultCount == 0}
        <div class="px-6 text-sm text-center py-14 sm:px-14">
          <slot name="not-found">
            <svg
              class="w-6 h-6 mx-auto text-gray-400"
              fill="none"
              viewBox="0 0 24 24"
              stroke-width="1.5"
              stroke="currentColor"
              aria-hidden="true"
              data-slot="icon"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                d="M12 9v3.75m-9.303 3.376c-.866 1.5.217 3.374 1.948 3.374h14.71c1.73 0 2.813-1.874 1.948-3.374L13.949 3.378c-.866-1.5-3.032-1.5-3.898 0L2.697 16.126ZM12 15.75h.007v.008H12v-.008Z"
              />
            </svg>
            <p class="mt-4 font-semibold text-gray-900 dark:text-gray-400">No results found</p>
            <p class="mt-2 text-gray-500">We couldn’t find anything with that term. Please try again.</p>
          </slot>
        </div>
      {:else}
        <ul
          class="p-4 pb-2 space-y-4 overflow-y-auto max-h-[calc(100%-55px)] sm:max-h-96 transform-gpu scroll-py-10 scroll-pb-2"
          id="options"
          role="listbox"
        >
          <slot name="search-results"></slot>
        </ul>
      {/if}
      <div
        class="hidden sm:flex flex-wrap items-center bg-gray-50 px-4 py-2.5 text-xs text-gray-700 dark:text-gray-300 dark:bg-gray-800"
      >
        <slot name="footer">
          Press
          <kbd
            class="flex items-center justify-center h-5 font-semibold text-gray-900 bg-white border border-gray-400 rounded dark:bg-gray-800 dark:text-gray-300 w-7 mp-1 sm:mx-2"
          >
            esc
          </kbd>
          to close
        </slot>
      </div>
    </div>
  </div>
</div>
