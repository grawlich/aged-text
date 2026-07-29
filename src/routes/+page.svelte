<script>
  // Components
  import AgedText from '../components/AgedText.svelte';


  // Sample data (for testing)
  let data = [
    {
      id: 'liajfdsa09453afs',
      post_body: `Holy cow, this post seems to be fading away!`,
      date_created: `2026-06-14T21:22:00.000Z`,
    },
    {
      id: 'gfdklsjfds8543kt',
      post_body: `Holy cow, this post seems to be fading away!`,
      date_created: `2026-07-01T21:22:00.000Z`,
    },
    {
      id: 'gfdgggfjfds8543kt',
      post_body: `Holy cow, this post seems to be fading away!`,
      date_created: `2026-07-18T21:22:00.000Z`,
    },
    {
      id: 'gffhgfjflkhl43kt',
      post_body: `Holy cow, this post seems to be fading away!`,
      date_created: `2026-07-25T21:22:00.000Z`,
    },
  ];


  // Settings for demo
  const randomizeOptions = [
    {
      label: "Letters",
      value: "letters",
    },
    {
      label: "Words",
      value: "words",
    },
    {
      label: "All",
      value: "all",
    },
  ];


  let randomize = $state("letters");
  let start = $state(100);
  let end = $state(10);
  let offset = $state(25);
  let text = $state("Holy cow, this post seems to be fading away!");
  let date_1 = $state();
  let date_2 = $state();
  let date_3 = $state();
  let date_4 = $state();
  let missingText = $state("?");
  let missingOpacity = $state(25);
  let lifespan = $state(60000 * 100000);
  let refreshInterval = $state(10000);


  function applyDemoText(text) {
    data.forEach((item)=>{
      item.post_body = text;
    });
  }
</script>


<!-- Post list -->
<ul class="post-list">
  <!-- Post cards -->
  {#each data.reverse() as post, i}
    <li class="post-card">
      <!-- Aging text item -->
      <AgedText
        data={post}
        class="post-text"
        {randomize}
        {start}
        {end}
        {offset}
        text="post_body"
        date="date_created"
        {missingText}
        {missingOpacity}
        {lifespan}
        {refreshInterval}
      />

      <!-- Date posted -->
      <p class="post-date">{new Date(post?.date_created).toLocaleDateString()}</p>
    </li>
  {/each}
</ul>


<style>
  .post-list {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: calc(var(--spacing) * 2);
  }


  .post-card {
    position: relative;
    width: 100%;
    max-width: 65ch;
    padding: calc(var(--spacing) * 4);
    background-color: light-dark(var(--color-surface-950), var(--color-surface-50));
    color: light-dark(var(--color-surface-contrast-950), var(--color-surface-contrast-50));
    font-weight: 600;
    display: flex;
    border-radius: calc(var(--spacing) * 3);
    flex-direction: column;
    gap: calc(var(--spacing) * 2);
    box-shadow: 0 0 0 0 transparent;
    transition: 200ms ease all;

    &::after {
      position: absolute;
      z-index: -1;
      content: "";
      inset: 0;
      border-color: var(--color-primary-500);
      box-sizing: border-box !important;
    }


    &:hover {
      transform: scale(1.025);
      box-shadow: 0 1rem 0 color-mix(in oklch, var(--color-black) 25%, transparent);
      
      &::after {
        border-width: 3px;
        transition: inherit;
      }
    }
    
    @supports (corner-shape: squircle) {
      corner-shape: squircle;
      border-radius: 100vh;
      
      &::after {
        corner-shape: inherit;
        border-radius: inherit;
      }
    }
  }


	/* Non-Tailwind classes must be in the global scope */
	/* You could also add this to the `app.css` file without `:global()` instead */
  :global(.post-text) {
    font-size: var(--text-lg);
    line-height: 1;
  }
</style>