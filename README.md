# Svelte Search Components

A Svelte library that provides components for building search functionalities, including `SearchDialog`, `SearchItem`, and `SearchGroup`.

## Table of Contents

- [Svelte Search Components](#svelte-search-components)
  - [Table of Contents](#table-of-contents)
  - [Requirements](#requirements)
  - [Installation](#installation)
  - [Usage](#usage)
    - [SearchDialog](#searchdialog)
      - [Props](#props)
      - [Events](#events)
      - [Slots](#slots)
      - [Example](#example)
    - [SearchItem](#searchitem)
      - [Props](#props-1)
      - [Events](#events-1)
      - [Slots](#slots-1)
      - [Example](#example-1)
    - [SearchGroup](#searchgroup)
      - [Props](#props-2)
      - [Slots](#slots-2)
      - [Example](#example-2)
  - [Example](#example-3)
  - [License](#license)

## Requirements

Before using this library, ensure you have the following installed in your project:

- [Svelte](https://svelte.dev/) - A component framework that compiles code to tiny, framework-less vanilla JS.
- [Tailwind CSS](https://tailwindcss.com/) - A utility-first CSS framework for rapid UI development.
- [@tailwindcss/forms](https://github.com/tailwindlabs/tailwindcss-forms) - A plugin that provides a basic reset for form styles.

## Installation

Install the library and its peer dependencies using npm:

```bash
npm install svelter-search-ui tailwindcss @tailwindcss/forms
```

**Note:** Make sure you have Svelte set up in your project. If not, you can create a new Svelte project

## Usage

Import the components into your Svelte application:

```svelte
<script>
  import { SearchDialog, SearchItem, SearchGroup } from 'svelter-search-ui';
</script>
```

### SearchDialog

`SearchDialog` is the main component that renders the search interface.

#### Props

- `show` (boolean, default: `false`): Controls the visibility of the search dialog.
- `placeholder` (string, default: `"Search..."`): Placeholder text for the search input.
- `resultCount` (number, default: `0`): The number of search results found.
- `isLoading` (boolean, default: `false`): Indicates if the search results are loading.
- `showRecent` (boolean, default: `true`): Determines if recent search items should be displayed when there's no input.
- `noRecentItemsText` (string, default: `"No recent searches to show"`): Text to display when there are no recent searches.
- `recentItemsGroupName` (string, default: `"Recent Searches"`): The name for the recent items group.

#### Events

- `search`: Dispatched when the search input changes (after a debounce of 350ms). The event detail contains `{ value }`.

#### Slots

- `recent-results`: Slot to customize the recent search results section.
- `no-recent-results`: Slot to customize the content when there are no recent searches.
- `info`: Slot to provide additional information or help content.
- `not-found`: Slot to customize the content when no search results are found.
- `search-results`: Slot to display the search results.
- `footer`: Slot to customize the footer content.

#### Example

```svelte
<script>
  import { SearchDialog } from 'svelter-search-ui';

  let showDialog = true;

  function handleSearch(event) {
    const searchValue = event.detail.value;
    // Perform search logic here
  }
</script>

<SearchDialog
  bind:show={showDialog}
  placeholder="Type to search..."
  on:search={handleSearch}
>
  <!-- Customize slots if needed -->
</SearchDialog>
```

### SearchItem

`SearchItem` represents an individual search result or suggestion.

#### Props

- `title` (string): The title of the search item.
- `url` (string, default: `"#"`): The URL associated with the item.
- `target` (string, optional): Specifies where to open the linked document (`"_blank"`, `"_self"`, etc.).
- `subtitle` (string, optional): A brief description or subtitle.
- `isRecentItem` (boolean, default: `false`): Indicates if the item is a recent search.

#### Events

- `select`: Dispatched when the search item is selected.

#### Slots

- `media`: Slot to customize the media/icon displayed alongside the item.

#### Example

```svelte
<SearchItem
  title="Item Title"
  subtitle="Item subtitle"
  url="/item-url"
  target="_blank"
  on:select={() => {
    // Handle item selection
  }}
>
  <!-- Customize the media slot if needed -->
  <div slot="media">
    <!-- Custom icon or image -->
  </div>
</SearchItem>
```

### SearchGroup

`SearchGroup` groups multiple `SearchItem` components under a common category.

#### Props

- `name` (string): The name of the group.
- `resultCount` (number, optional): The number of items in the group.

#### Slots

- **Default Slot**: Place your `SearchItem` components here.

#### Example

```svelte
<SearchGroup name="Group Name" resultCount={2}>
  <SearchItem title="Item 1" />
  <SearchItem title="Item 2" />
</SearchGroup>
```

## Example

Here's a basic example of how to use the components together:

```svelte
<script>
  import { SearchDialog, SearchItem, SearchGroup } from 'svelter-search-ui';
  let showDialog = true;

  let searchResults = [];
  let isLoading = false;

  function handleSearch(event) {
    const searchValue = event.detail.value;
    isLoading = true;

    // Simulate async search operation
    setTimeout(() => {
      searchResults = [
        { title: 'Apple', subtitle: 'A sweet red fruit' },
        { title: 'Banana', subtitle: 'A long yellow fruit' },
      ];
      isLoading = false;
    }, 1000);
  }
</script>

<SearchDialog
  bind:show={showDialog}
  isLoading={isLoading}
  resultCount={searchResults.length}
  on:search={handleSearch}
>
  <ul slot="search-results">
    <SearchGroup name="Fruits" resultCount={searchResults.length}>
      {#each searchResults as result}
        <SearchItem
          title={result.title}
          subtitle={result.subtitle}
          on:select={() => (showDialog = false)}
        />
      {/each}
    </SearchGroup>
  </ul>
</SearchDialog>
```

## License

This project is licensed under the [MIT License](LICENSE).