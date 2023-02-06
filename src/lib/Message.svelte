<script lang="ts">
  import { onMount, onDestroy, afterUpdate } from "svelte";
  import { currentUser, pb } from "./pocketbase";
  import Bubble from "./bubble.svelte";

  let messages = [];
  let newMessage: string;
  let messageContainer;
  let unsubscribe: () => void;
  onMount(async () => {
    const resultList = await pb.collection("messages").getList(1, 50, {
      sort: "created",
      expand: "user",
    });
    messages = resultList.items;

    // subscribe to realtime message
    unsubscribe = await pb
      .collection("messages")
      .subscribe("*", async ({ action, record }) => {
        if (action === "create") {
          // Fetch associated user
          const user = await pb.collection("users").getOne(record.user);
          record.expand = { user };
          messages = [...messages, record];
        }
        if (action === "delete") {
          messages = messages.filter((m) => m.id !== record.id);
        }
      });
  });

  onDestroy(() => {
    unsubscribe();
  });

  afterUpdate(() => {
    scrollToBottom(messageContainer);
  });

  const scrollToBottom = async (node) => {
    node.scroll({ top: node.scrollHeight, behavior: "smooth" });
  };

  async function sendMessage() {
    const data = {
      text: newMessage,
      user: $currentUser.id,
    };
    const createdMessage = await pb.collection("messages").create(data);
  }
</script>

<div class="container">
  <div class="chatbox">
    <div bind:this={messageContainer} class="message-container">
      {#each messages as message}
        <Bubble {message} />
      {/each}
    </div>
  </div>
</div>

<form on:submit|preventDefault={sendMessage}>
  <input type="text" placeholder="Message" bind:value={newMessage} />
  <button type="submit">Send it!</button>
</form>

<style>
  .message-container {
    overflow-y: scroll;
    height: 25rem;
  }

  .toptext {
    background-color: rgb(250, 144, 23);
    color: white;
    text-align: center;
    border-top-left-radius: 0.5rem;
    border-top-right-radius: 0.5rem;
    margin-bottom: 0;
  }

  .chatbox {
    width: 20rem;
    margin: auto;
    border-radius: 0.5rem;
    box-shadow: rgba(0, 0, 0, 0.24) 0px 3px 8px;
    background-color: black;
  }

  .container {
    width: 100%;
  }
</style>
