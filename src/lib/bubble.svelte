<script>
  export let message; // Message object that contains all information about this specific message
  import { currentUser } from "./pocketbase";
</script>

<p
  class={message.expand?.user?.username == $currentUser.username
    ? "info-right"
    : "info-left"}
>
  {message.expand?.user?.username} | {message.created}
</p>

<div
  class={message.expand?.user?.username == $currentUser.username
    ? "bubble-right"
    : "bubble-left"}
>
  <p class="message">{message.text}</p>
</div>

<style>
  .bubble-left,
  .bubble-right {
    width: 80%;
    margin: 0.5rem;
    border-radius: 1rem;
    padding: 0.25rem;
  }

  .info-left {
    float: left;
  }

  .info-right {
    float: right;
  }

  .info-left,
  .info-right {
    font-size: 0.6rem;
    margin: 0;
    padding: 0;
    margin-left: 0.7rem;
    margin-top: 1rem;
    opacity: 50%;
  }

  .bubble-left {
    background-color: grey;
    float: left;
    border-bottom-left-radius: 0;
  }

  .bubble-right {
    float: right;
    background-color: rgb(98, 98, 197);
    border-bottom-right-radius: 0;
  }

  .message {
    font-size: 0.7rem;
  }
</style>
