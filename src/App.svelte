<script lang="ts">
  import { currentUser } from "./lib/pocketbase";
  import Login from "./lib/Login.svelte";
  import Messages from "./lib/Message.svelte";
</script>

<h1>PocketChat</h1>
<Login />

{#if $currentUser}
  <Messages />
{/if}
