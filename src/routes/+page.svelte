<script>
	// Components
  import AgedText from '../components/AgedText.svelte'


  // Sample data (for testing)
  const data = [
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
  ]
</script>


<!-- Post list -->
<ul class="post-list">
  <!-- Post cards -->
  {#each data as post}
    <li class="post-card">
      <!-- Aging text item -->
      <AgedText
        data={post}
        class="post-text"
        randomize="letters"
        start={100}
        end={0}
        offset={25}
        text="post_body"
        date="date_created"
        missingText="?"
        missingOpacity={50}
        lifespan={60000 * 100000}
        refreshInterval={10000}
      />
    </li>
  {/each}
</ul>


<style>
  .post-list {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: calc(var(--spacing) * 4);
  }


  .post-card {
    width: fit-content;
    padding: calc(var(--spacing) * 4);
    background-color: light-dark(var(--color-surface-950), var(--color-surface-50));
    color: light-dark(var(--color-surface-contrast-950), var(--color-surface-contrast-50));
    font-weight: 600;
    display: flex;
    border-radius: calc(var(--spacing) * 3);
    flex-direction: column;
    align-items: center;
    gap: calc(var(--spacing) * 8);
    box-shadow: 0 0 0 0 transparent;
    transition: 150ms ease all;

    &:hover {
      transform: scale(1.05);
      box-shadow: 0 .5rem 0 color-mix(in oklch, var(--color-black) 25%, transparent);
    }
    
    @supports (corner-shape: squircle) {
      corner-shape: squircle;
      border-radius: 100vh;
    }
  }


	/* Non-Tailwind classes must be in the global scope */
	/* You could also add this to the `app.css` file without `:global()` instead */
  :global(.post-text) {
    line-height: 1;
  }
</style>