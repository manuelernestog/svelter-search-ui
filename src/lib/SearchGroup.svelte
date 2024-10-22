<script>
  export let name, resultCount;
</script>

{#if resultCount != 0}
  <li class="p-2">
    <h2 class="text-xs font-semibold text-gray-900 dark:text-gray-200">{name}</h2>
    <ul class="mt-2 -mx-4 text-sm text-gray-700 dark:text-gray-400">
      <slot></slot>
    </ul>
  </li>
{/if}
