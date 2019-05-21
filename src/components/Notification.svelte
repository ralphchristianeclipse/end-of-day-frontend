<script>
  import { fade } from "svelte/transition";
  export let message = "";
  export let type = "success";
  const colorTypes = {
    success: "#33ff00",
    error: "#ee2200",
    warning: "gold"
  };
  $: color = colorTypes[type] || "white";
</script>

<style>
  .notification {
    padding: 10px;
    color: #fff;
    margin-bottom: 10px;
    font-weight: bold;
    text-transform: uppercase;
    border-radius: 5px;
  }
</style>

<div class="notification" style="background: {color}" transition:fade>
   {message}
</div>
