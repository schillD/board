<script lang="ts">
  import { currentUser, pb } from "../pocketbase/pocketbase";
  import { Badge, Form, FormGroup, Input, Label } from "sveltestrap";
  import "bootstrap/dist/css/bootstrap.min.css";
  import { Button } from "sveltestrap";

  let post_title: string;
  let post_body: string;

  async function post() {
    if ($currentUser != null) {
      const data = {
        user: $currentUser.id,
        post_title: post_title,
        post_body: post_body,
      };

      const record = await pb.collection("posts").create(data);
    } else {
      alert("CurrentUser ist Null");
    }
  }
</script>

<div class="container">
  <div class="alert alert-dark" role="alert">
    <Form>
      <FormGroup floating label="Titel">
        <Input placeholder="Titel" required bind:value={post_title} />
      </FormGroup>

      <FormGroup>
        <div class="form-floating">
          <textarea
            class="form-control"
            placeholder="Body"
            bind:value={post_body}
            id="floatingTextarea2"
            style="height: 100px"
          />
          <label for="floatingTextarea2">Text</label>
        </div>
      </FormGroup>

      <Button on:click={post}>Post</Button>
    </Form>
  </div>
</div>

<style>
  .container {
    max-width: 700px;
    margin: 0 auto;
    padding: 2rem;
    text-align: center;
  }
  textarea {
    width: 100%;
    height: 300px;
  }

  .container {
    padding: 10px 16px;
    margin: 40px auto;
    max-width: 800px;
  }
</style>
