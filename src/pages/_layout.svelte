<script lang="ts">
  import Hf from '../components/HF.svelte';
  import Sidebar from '../components/Sidebar.svelte';
  import { params } from '@sveltech/routify';
  let subreddit = '';
  $: subreddit = $params.subreddit;

  import { metatags } from '@sveltech/routify';
  metatags.title = `SpeedDial${subreddit ? ` – ${subreddit}` : ''}`;
  metatags.description =
    'An experimental, open source Reddit client built with Svelte.';
</script>

<style>
  :global(body) {
    margin: 0;
    padding: 0;
    color: #c4c4c4;
  }
  .container {
    display: flex;
    min-height: 100vh;
    background: #0d0f10;
  }
  header {
    position: fixed;
    width: 14rem;
    overflow-y: scroll;
    height: 100vh;
    background: #201f1f;
  }
  main {
    margin: 0;
    padding: 4rem 4rem;
    padding-left: 18rem;
    max-width: 100vw;
  }
</style>

<div class="container">
  <header>
    <Sidebar />
  </header>
  <main>
    <slot />
  </main>
  <Hf />
</div>
