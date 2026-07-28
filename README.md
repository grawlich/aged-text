# Aging Text


This is a small proof-of-concept built using:

- [SvelteKit (Svelte 5)](https://github.com/sveltejs/kit) (Front-end UI)
- [TailwindCSS](https://github.com/tailwindlabs/tailwindcss) (CSS Styles)
- [Prando](https://github.com/zeh/prando?tab=License-1-ov-file) (Seeded RNG)


> [!WARNING]
> Due to this merely being a proof-of-concept, no support is provided at this time. Updates will come as I deem necessary, but for the foreseeable future, this is how the component will remain.


### Purpose:


Displays a faded string based on when the text was created and the maximum lifetime.


---


### How to use in your own project


The code for fading the text is contained entirely within the Svelte component, minus the `Prando` dependency. All you need to do is simply copy/paste the `AgedText.svelte` component into your project, install `Prando` with `pnpm add prando` or `npm i prando`, and import the `AgedText.svelte` component into the location you wish to use it.


The component only outputs a paragraph `<p>` tag with spans `<span>` around the text chunks.

```
<p>
  <span style="opacity: 42%;">Just</span>
  <span style="opacity: 69%;">three</span>
  <span style="opacity: 22%;">words.</span>
</p>
```


By default, it is looking for an array of JSON objects containing values for `id`, `text`, and `date`.


It should all be at the top-level, and look similar to this:

``` javascript
const data = [
  {
    id: 'liajfdsa09453afs', // The ID of the item (must be unique)
    text: `Holy cow, this post seems to be fading away!`, // The text to fade
    date: `2026-06-14T21:22:00.000Z`, // The date the text was originally created (ISO Date)
  },
  // More objects...
]
```


Those objects can then be passed as a value to the `data` props of the component.

```svelte
{#each data as item}
  <AgedText data={item} />
{/each}
```


The component is looking for specific key values by default, but those can be manually adjusted via props:

```javascript
const customData = [
  {
    post_id: 'liajfdsa09453afs', // The ID of the item (must be unique)
    post_body: `Holy cow, this post seems to be fading away!`, // The text to fade
    date_created: `2026-06-14T21:22:00.000Z`, // The date the text was orginally created (ISO Date)
  },
  // More objects...
]
```


```svelte
{#each customData as item}
  <AgedText
    data={item}
    id="post_id"
    text="post_text"
    date="date_created"
  />
{/each}
```


This effectively just tells the component to look for a specified object key, it can be anything you want, as long as it exists and is a valid data type.


Additionally, you can change whether it fades individual letters, whole words, or the entire string at once!


Once again, just set the prop for `randomize` and set the appropriate value:

- `letters` - *default*; Sets the component to change individual letter opacity.
- `words` - Sets the component to change the opacity of whole words.
- `(any)` - Any other value will set the component to change the opacity of the entire string at once.


Usage:

```svelte
{#each customData as item}
  <AgedText
    data={customData}
    randomize="words"
  />
{/each}
```


---


### Anatomy


Props:

`data` - The data object for this text block.
`class` - Any arbitrary CSS classes (as long as they are in the global scope).
`randomize` - *default: "letters";* Expects a value of "letters" or "words", any other value fades the entire block evenly.
`start` - *default: 100;* A starting value for the opacity, expected value between 0 and 100.
`end` - *default: 10;* An ending value for the opacity, expected value between 0 and 100.
`id` - *default: "id";* A value to match within the object keys to find a specific value.
`text` - *default: "text";* The text you intend to affect the opacity of.
`date` - *default: "date";* The date the text was created to have a fixed starting point.
`lifespan` - *default: 60000 (1 minute in milliseconds);* - The time before the text is completely faded based on when it was created.
`refreshInterval` - *default: 1000 (1 second in milliseconds);* - The time between opacity updates.
`missingText` - *default: "?";* - A character to replace "missing" text parts with.
`missingOpacity` - *default: -1 (disabled);* - The opacity threshold for considering the text parts "missing".


Example:

```svelte
<AgedText
  data={item}
  class="custom-class"
  randomize="words"
  baseOpacity={80}
  id="id"
  text="post_body"
  date="date_created"
  lifespan={60000 * 100000}
  refreshInterval={10000}
  missingOpacity={10}
  missingText="..."
/>
```


---


### Other frameworks

This is a very basic component, all things considered. Most of the non-reactive code should just work in other frameworks that support JavaScript ES modules.

For the output, simply use your framework's own syntax to create the required reactivity and HTML output. Many frameworks offer similar loops and conditional blocks.

I will update this section at a later time with more in-depth examples if folks really want a guide for their specific framework.


---


### License


Code is MIT licensed

Copyright © 2026 [grawlich](https://github.com/grawlich)