<script lang="ts">
  import { pb, currentUser } from "../../pocketbase/pocketbase";
  import { Form, FormGroup } from "sveltestrap";
  import "bootstrap/dist/css/bootstrap.min.css";
  import { Button } from "sveltestrap";
  import { onMount } from "svelte";

  export let data: any;
  let post: any = [];
  let username: string;
  let userID: string;
  let comment_text: string;
  let comment_list: any = [];
  let showPosts: number = 5;

  async function post_comment() {
    if ($currentUser != null) {
      const createData: any = {
        user: $currentUser.id,
        post: data.postId,
        comment_text: comment_text,
      };

      const record = await pb.collection("comments").create(createData);
      getCommentList();
    } else {
      alert("CurrentUser ist Null");
    }
  }

  async function getOnePost() {
    const record: any = await pb.collection("posts").getOne(data.postId, {
      expand: "user",
    });
    post = record;
    username = record.expand.user.username;
    userID = record.expand.user.id;
    console.log(post);
  }
  getOnePost();

  async function getCommentList() {
    const resultList = await pb.collection("comments").getList(1, showPosts, {
      expand: "user,post",
      filter: "post.id~" + "'" + data.postId + "'",
    });
    comment_list = resultList.items;
  }
  getCommentList();

  onMount(async () => {
    window.onscroll = function (ev) {
      if (
        window.innerHeight + window.pageYOffset >=
        document.body.offsetHeight
      ) {
        showPosts += 5;
        getCommentList();
      }
    };
  });
</script>

<div class="container">
  <div class="alert alert-dark">
    <h2>Title: {post.post_title}</h2>
    <p1>Body: {post.post_body}</p1><br />
    <br />
    <p2>Created: {post.created}</p2><br />
    <br />
    <p><a href={"/profile/" + userID}>Author: {username}</a></p>
    <br />

    <Form>
      <FormGroup>
        <h3>Comment:</h3>
        <div class="form-floating">
          <textarea
            class="form-control"
            placeholder="Body"
            bind:value={comment_text}
            id="floatingTextarea2"
            style="height: 100px"
          />
          <label for="floatingTextarea2">Text</label>
        </div>
      </FormGroup>
      <div class="button">
        <Button on:click={post_comment}>Post Comment</Button>
      </div>
    </Form>
  </div>

  {#each comment_list as comment (comment.id)}
    <div class="alert alert-dark">
      <p2>Body: {comment.comment_text}</p2><br />
      <br />
      <p>
        <a href={"/profile/" + comment.expand.user.id}
          >Author: {comment.expand.user.username}</a
        >
      </p>
    </div>
  {/each}
</div>

<style>
  .container {
    padding: 10px 16px;
    margin: 40px auto;
    max-width: 800px;
  }

  .container h2 {
    font-size: 30px;
    color: #3538f1d0;
    margin-bottom: 8px;
  }
  .container p1 {
    font-size: 20px;
  }
  .container a {
    text-decoration: none;
    color: inherit;
  }
  .button {
    text-align: center;
  }
</style>
