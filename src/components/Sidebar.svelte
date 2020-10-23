<script lang="ts">
  import { isActive, url, goto } from '@sveltech/routify';

  let links = [
    ['./r/all', 'all'],
    ['./r/popular', 'popular'],
  ];

  let inputtedSubreddit;

  const onSubmitSubreddit = (e) => {
    e.preventDefault();
    if (
      links.filter((link) => {
        return link[1].toLowerCase() === inputtedSubreddit.toLowerCase();
      }).length == 0
    )
      links = [
        [`./r/${inputtedSubreddit}`, inputtedSubreddit],
        ...links,
      ].sort((a, b) => (a[1].toLowerCase() < b[1].toLowerCase() ? -1 : 1));
    $goto($url(`./r/${inputtedSubreddit}`));
    inputtedSubreddit = '';
  };

  fetch(`https://www.reddit.com/subreddits/.json`)
    .then((res) => res.json())
    .then((value) => {
      const subreddits = value.data.children.map((subreddit) => [
        `./r/${subreddit.data.display_name}`,
        subreddit.data.display_name,
      ]);
      links = [...links, ...subreddits].sort((a, b) =>
        a[1].toLowerCase() < b[1].toLowerCase() ? -1 : 1
      );
    });
</script>

<style>
  nav {
    position: relative;
  }
  ul {
    padding: 0;
    list-style: none;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: start;
  }
  li {
    padding: 0.25rem 1rem;
  }
  nav > div > a {
    background: #1a1a1b;
  }
  nav > div {
    position: absolute;
    top: 0;
    left: 0;
    width: 14rem;
  }
  form {
    display: flex;
    align-items: center;
    justify-content: center;
    padding-top: 4rem;
  }
  input {
    width: 12rem;
    height: 2rem;
    background: #030303;
    border: none;
    color: #c4c4c4;
    margin: 0;
    padding: 0 0.5rem;
  }
  h1 {
    margin: 0;
    padding: 1rem 0;
    text-align: center;
  }
  a {
    text-decoration: none;
    width: 100%;
    color: inherit;
  }
  .active {
    background: #333131;
  }
  a:hover {
    background: #333131;
  }
</style>

<nav>
  <div>
    <a href="/">
      <h1>SpeedDial</h1>
    </a>
  </div>
  <form on:submit={onSubmitSubreddit}>
    <input
      name="subreddit"
      type="text"
      placeholder="Search..."
      bind:value={inputtedSubreddit} />
  </form>
  <ul>
    {#each links as [path, name]}
      <a href={$url(path)} class:active={$isActive(path)}>
        <li>{name}</li>
      </a>
    {/each}
  </ul>
</nav>
