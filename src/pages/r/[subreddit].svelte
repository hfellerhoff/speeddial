<script lang="ts">
  import { slide } from 'svelte/transition';
  import { params } from '@sveltech/routify';
  let subreddit = '';
  $: subreddit = $params.subreddit;

  let redditData: Promise<RedditData>;

  $: redditData = fetch(
    `https://www.reddit.com/r/${subreddit}/.json`
  ).then((res) => res.json());

  interface RedditData {
    data: {
      children: {
        data: {
          thumbnail: string;
          title: string;
          subreddit: string;
          score: number;
          url: string;
          preview: {
            images: {
              resolutions: {
                [key: number]: {
                  url: string;
                  width: number;
                  height: number;
                };
              };
            }[];
          };
        };
      }[];
    };
  }
</script>

<style>
  article {
    position: relative;
    display: flex;
    margin: 2rem 0;
  }
  a {
    text-decoration: none;
    color: #c4c4c4;
  }
  a:hover {
    color: #606fe0;
  }
  h1 {
    margin: 0;
    padding: 0;
  }
  a > h2 {
    margin: 0;
    padding: 0;
    font-size: 1.15rem;
  }
  .content {
    margin-left: 6rem;
  }
  .nomargin {
    margin-left: 0;
  }
  .thumbnail {
    position: absolute;
    height: 4rem;
    width: 4rem;
    margin-right: 2rem;
    background: #1a1a1b;
    display: flex;
    align-items: stretch;
    justify-content: center;
    left: 0;
    top: 0;
  }
  .thumbnail > div {
    display: flex;
    align-items: center;
    justify-content: center;
    font-weight: 700;
  }
  img {
    object-fit: cover;
    transition: 0.2s;
    z-index: 1;
  }
  img:hover {
    height: initial;
    width: 24rem;
    z-index: 2;
  }
  .article-details {
    margin-top: 0.25rem;
  }
</style>

{#await redditData}
  {#if subreddit}
    <div>
      <a href={`https://www.reddit.com/r/${subreddit}`}>
        <h1 in:slide>{subreddit}</h1>
      </a>
      <p>loading...</p>
    </div>
  {/if}
{:then value}
  {#if subreddit}
    <a href={`https://www.reddit.com/r/${subreddit}`}><h1 out:slide>
        {subreddit}
      </h1></a>
  {/if}

  {#each value.data.children as post}
    <article transition:slide>
      {#if post.data.thumbnail}
        {#if post.data.thumbnail === 'self' || post.data.thumbnail === 'nsfw'}
          <div class="thumbnail">
            <div>{post.data.thumbnail.toUpperCase()}</div>
          </div>
        {:else if post.data.thumbnail === 'default'}
          <div class="thumbnail" />
        {:else}
          <img
            class="thumbnail"
            src={post.data.url.includes('i.redd.it') || post.data.url.includes('i.imgur.com') ? post.data.url : post.data.thumbnail}
            alt={post.data.title} />
        {/if}
      {/if}
      <div class="content" class:nomargin={!post.data.thumbnail}>
        <a href={post.data.url}><h2>{post.data.title}</h2></a>
        <div class="article-details">
          <a
            href={`https://www.reddit.com/r/${post.data.subreddit}`}><span>{post.data.subreddit}</span></a>
          <span>• {post.data.score.toLocaleString()}</span>
        </div>
      </div>
    </article>
  {/each}
{:catch error}
  <pre>{error}</pre>
{/await}
