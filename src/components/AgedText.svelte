<script>
  // Svelte utilities
  import { onMount } from "svelte";


  // Utilities
  import Prando from 'prando';


  // Props
  let {
    data, // The full data object for a single text item
    class: className, // Arbitrary CSS classes for styling
    randomize = "letters", // Type of randomization; default: "letters"
    start = 90, // Starting opacity; default: 100
    end = 10, // Ending opacity; default: 10
    offset = 25, // How much value deviation the fade is allowed ot have.
    id = "id", // Key to match for unique ID; default: "id"
    text = "text", // Key to match for text contents; default: "text"
    date = "date", // Key to match for created date; default: "date"
    lifespan = 60000, // How long until the text fully fades; default: 60000 (1 minute in ms)
    refreshInterval = 1000, // How long to wait to update opacity value; default: 1000 (1 second in ms)
    missingOpacity = -1, // Opacity value required to appear as missing; default: -1 (disabled)
    missingText = "?", // Can replace text below a certain opacity threshold; default: "?"
  } = $props();


  // Destructure data
  let text_id = $derived(data[id]);
  let text_body = $derived(data[text]); 
  let date_created = $derived(new Date(data[date]).getTime());


  // Prepare to store post contents
  let contents = $state([]);


  // Setup seeded RNG
  function RNG(seed) {
    return new Prando(seed).next(-1, 1);
  }


  // Generate random opacity value
  function generateRandomOpacity(currentAge, seed) {
    if (currentAge === 0) return start;

    if (currentAge > lifespan) return end;

    const progress = currentAge / lifespan;
    const base = start + (end - start) * progress;

    const weight = 1 - Math.abs(progress * 2 - 1);

    const baseOffset = (RNG(seed) * offset) - (offset / 2);
    const totalOffset = baseOffset * weight;

    return Math.min(Math.max(base + totalOffset, end), start);
  }


  // Replace character values based on threshold
  function swapCharacters(string, value) {
    if (Number(missingOpacity) > 0 && Number(missingOpacity) >= value) {
      if (typeof string === "string" && string.length > 0) {
        return Array.from(string, (val) => {
          if (val != " ") return missingText;
          return val;
        }).join('');
      }

      return missingText;
    }

    return string;
  };


  // Set text opacity
  function setOpacity() {
    // Update current time
    let current_time = new Date().getTime();

    // Set age state
    let age = 0;

    // Get current age
    if (current_time - date_created < lifespan) {
      age = current_time - date_created;
    } else {
      age = lifespan;
    }
    
    // Prepare to generate the result
    let result;
    
    // Set fade type
    if (randomize === "letters") {
      // Get post parts
      let letters = text_body.split("");

      // Return values
      result = letters.map((char, i) => {
        const opacity = generateRandomOpacity(age, text_id + i);
        return {
          part: swapCharacters(char, opacity),
          opacity: opacity,
        };
      });
    }
    else if (randomize === "words") {
      // Get post parts
      let words = text_body.split(" ");

      // Return values
      result = words.map((word, i) => {
        const opacity = generateRandomOpacity(age, text_id + i);
        return {
          part: swapCharacters(word, opacity),
          opacity: opacity,
        };
      });
    }
    else {
      const opacity = generateRandomOpacity(age, text_id);
      result = {
        part: swapCharacters(text_body, opacity),
        opacity: opacity,
      };
    }

    // Return result
    return { result };
  }


  // Generate title/aria-label for accessibility
  function generateLabel(value) {
    if (randomize === "letters") {
      // If setting letter opacity, map each letter to an array
      let res = [];
      value.forEach(item => {
        res.push(item?.part);
      });
      return res.join("");
    }
    else if (randomize === "words") {
      // If setting word opacity, map each word to an object
      let res = [];
      value.forEach(item => {
        res.push(item?.part);
      });
      return res.join(" ");
    }
    else {
      // If any other value, pass the entire string
      return value?.part;
    }
  };


  // Convert the text on component render
  onMount(()=>{
    // Set initial value
    contents = setOpacity();

    // Set a recurring update
    if (refreshInterval > 0) {
      setInterval(() => {
        contents = setOpacity();
      }, refreshInterval);
    }
  });
</script>


<!-- Text container -->
<p class={className} title={generateLabel(contents?.result ?? [])} aria-label={generateLabel(contents?.result ?? [])}>
  <!-- Fade types -->
  <!-- Fade by letter -->
  {#if randomize === "letters"}
    {#each contents.result as letter, i}
      <span style="opacity: {letter?.opacity}%;" aria-hidden="true">{letter?.part}</span>
    {/each}

  <!-- Fade by words -->
  {:else if randomize === "words"}
    {#each contents.result as words, i}
      <span style="opacity: {words?.opacity}%;" aria-hidden="true">{words?.part + " "}</span>
    {/each}

  <!-- Fade entire string (fallback) -->
  {:else}
    <span style="opacity: {contents?.result?.opacity}%;" aria-hidden="true">{contents?.result?.part}</span>
  {/if}
</p>


<!--
@component
## AgedText
Displays a faded string based on when the text was created and the maximum lifetime.


### Prop Definitions
- `data` - The data object for this text block.
- `class` - Any arbitrary CSS classes (as long as they are in the global scope).
- `randomize` - *default: "letters";* Expects a value of "letters" or "words", any other value fades the entire block evenly.
- `start` - *default: 100;* A starting value for the opacity, expected value between 0 and 100.
- `end` - *default: 10;* An ending value for the opacity, expected value between 0 and 100.
- `id` - *default: "id";* A value to match within the object keys to find a specific value.
- `text` - *default: "text";* The text you intend to affect the opacity of.
- `date` - *default: "date";* The date the text was created to have a fixed starting point.
- `lifespan` - *default: 60000 (1 minute in milliseconds);* - The time before the text is completely faded based on when it was created.
- `refreshInterval` - *default: 1000 (1 second in milliseconds);* - The time between opacity updates.
- `missingText` - *default: "?";* - A character to replace "missing" text parts with.
- `missingOpacity` - *default: -1 (disabled);* - The opacity threshold for considering the text parts "missing".


@example
<AgedText
  data={item}
  class="custom-class"
  randomize="words"
  start={80}
  end={20}
  id="id"
  text="post_body"
  date="date_created"
  lifespan={60000 * 100000}
  refreshInterval={10000}
  missingText="..."
  missingOpacity={10}
/>
-->