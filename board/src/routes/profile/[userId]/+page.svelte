<script lang="ts">
	import { onMount } from "svelte";
    import {pb} from "../../pocketbase/pocketbase";
    export let data:any;
    let user:any = [];
    let postList:any = [];
    let showPosts = 5;




    async function updateAvatar() {
      const data = {
    "username": "test_username_update",
    "emailVisibility": false,
    "password": "87654321",
    "passwordConfirm": "87654321",
    "oldPassword": "12345678",
    "name": "test"
};
const record = await pb.collection('users').update('RECORD_ID', data);


    }

    async function getUserDetails() {
        const record = await pb.collection('users').getOne(data.userId, {
        });
        user = record;
    
    }
    getUserDetails();

    
    async function getPostList() {
        const resultList = await pb.collection('posts').getList(1, showPosts, {
      '$autoCancel': false,
      expand: 'user',
      filter: "user.id=" + "'"+data.userId+"'"
    });
    postList = resultList.items;
    }
    getPostList();

    
    onMount( async() => {
      window.onscroll = function(ev) {
      if ((window.innerHeight + window.pageYOffset ) >= document.body.offsetHeight) {
        showPosts += 5;
        getPostList(); 
      }

      };

    });
    
    </script>
    
    <div class="container">
    <div class="alert alert-dark" >
      <label>
        <img src="/default_avatar.png" alt="nutzer avatar" width="200" height="300" />
        <input type="file" name="avatar" value="" accept="image/*" hidden>
      </label>
        <h2>Username: {user.username}</h2>
        <h2>Joined since: {user.created} </h2>
        
      </div>
    </div>
    <div class="profilePostList">
    {#each postList as post (post.id)}

    <div class="container">
    <div class="alert alert-dark">
      <a href={"/post/" + post.id }>
        <h2> Title: {post.post_title}</h2>
        <p2>Body: {post.post_body}</p2><br />
        <br />
        <p2>Created: {post.created}</p2><br />
        <br />
        <p><a href={"/profile/" + post.expand.user.id}>Author: {post.expand.user.username}</a></p>
        </div>
        </div>
  {/each}

    </div>


<style>
      .container {
  max-width: 700px;
  margin: 0 auto;
  padding: 2rem;
  text-align: center;

}
.profilePostList a {
  text-decoration: none;
  color: inherit;
}
</style>
 
    
    
    