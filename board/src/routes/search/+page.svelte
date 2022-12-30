<script lang="ts">
    import 'bootstrap/dist/css/bootstrap.min.css';
    import {pb} from "../pocketbase/pocketbase";
    import {
    Button,
    Form,
    FormGroup,
    Input
  } from 'sveltestrap';

  let input:string;
  let searchList:any = [];
  let searchType:string;

  async function search_post() {
    searchType = "Title";
    const resultList:any = await pb.collection('posts').getFullList(50, {
    filter: "post_title~" + "'"+input+"'" +"||"+ "post_body~" + "'"+input+"'" ,
    expand: "user",
});
searchList = resultList;
  }

  async function search_user() {
    searchType = "User"
    const resultList = await pb.collection('users').getList(1, 50, {
      filter: "username~" + "'"+input+"'",
});
searchList = resultList.items;
}
</script>

<div class="container">
<div class="alert alert-dark" role="alert">

<h1>Suche</h1>

<Form>
    <FormGroup floating label="Gib hier Suchwörter ein">
      <Input required bind:value={input} />
    </FormGroup>
    
    {#if input } 
    <Button on:click={search_post}> Search Post </Button>
    <Button on:click={search_user}> Search User </Button>
    {:else}
    <div class="alert alert-info" role="alert">
      Suchfeld darf nicht leer sein!
    </div>
    

    {/if}
</Form>
<br />


{#if searchType=="Title"}

  {#each searchList as post (post.id)}
  <div class="alert alert-dark" role="alert">
  
      <a href={"/post/" + post.id }>
      <h2> Title: {post.post_title}</h2>
      <p2>Body: {post.post_body}</p2><br />
      <br />
      <p2>Created: {post.created}</p2><br />
      <br />
      <p><a href={"/profile/" + post.expand.user.id}>Author: {post.expand.user.username}</a></p>
      </a>
    </div>
  {/each}

{/if}


{#if searchType=="User"}
  
  {#each searchList as user (user.id)}
  <a href={"/profile/" + user.id }> <br>
  <h2>Username: {user.name} </h2>
  </a>


  {/each}

{/if}

</div>
</div>

<style>
    .container {
  max-width: 700px;
  margin: 0 auto;
  padding: 2rem;
  text-align: center;

}
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