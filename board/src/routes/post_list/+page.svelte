<script lang="ts">
  import { pb, currentUser } from "../pocketbase/pocketbase";
  import { onMount } from "svelte";

  let postList: any = [];
  let showPosts = 5;

  async function getPostList() {
    const resultList = await pb.collection("posts").getList(1, showPosts, {
      expand: "user",
    });
    postList = resultList.items;
  }

  onMount(async () => {
    window.onscroll = function (ev) {
      if (
        window.innerHeight + window.pageYOffset >=
        document.body.offsetHeight
      ) {
        showPosts += 5;
        getPostList();
      }
    };
  });

  getPostList();
</script>

{#each postList as post (post.id)}
  <div class="container">
    <div class="alert alert-dark">
      <a href={"/post/" + post.id}>
        <h2>Title: {post.post_title}</h2>
        <p2>Body: {post.post_body}</p2><br />
        <br />
        <p2>Created: {post.created}</p2><br />
        <br />
        <p>
          <a href={"/profile/" + post.expand.user.id}
            >Author: {post.expand.user.username}</a
          >
        </p>
      </a>
    </div>
  </div>
{/each}

<style>
  .container {
    padding: 10px 16px;
    margin: 40px auto;
    max-width: 800px;
  }

  .container h2 {
    font-size: 20px;
    color: #3538f1d0;
    margin-bottom: 8px;
  }

  .container a {
    text-decoration: none;
    color: inherit;
  }
</style>
