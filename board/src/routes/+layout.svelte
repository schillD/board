<script lang="ts">
  import "bootstrap/dist/css/bootstrap.min.css";
  import "./pocketbase/login/+page.svelte";
  import { currentUser, pb } from "./pocketbase/pocketbase";

  import {
    Collapse,
    Navbar,
    NavbarToggler,
    NavbarBrand,
    Nav,
    NavItem,
    NavLink,
    Dropdown,
    DropdownToggle,
    DropdownMenu,
    DropdownItem,
    Container,
    Button,
  } from "sveltestrap";

  function signOut() {
    pb.authStore.clear();
    console.log("test");
  }
</script>

<Navbar color="light" light expand="md">
  <NavbarBrand href="/">GHSE-Board</NavbarBrand>
  <Nav class="ms-auto">
    {#if $currentUser}
      <NavItem>
        <NavLink href={"/profile/" + $currentUser.id}>Profile</NavLink>
      </NavItem>
      <NavItem>
        <NavLink href="/create_post">Create New Post</NavLink>
      </NavItem>
    {/if}
    <NavItem>
      <NavLink href="/search">Search</NavLink>
    </NavItem>
    <NavItem>
      {#if $currentUser}
        <Button on:click={signOut}>Sign Out</Button>
      {/if}
    </NavItem>
  </Nav>
</Navbar>

<slot />
