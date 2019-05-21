<script>
  import { createEventDispatcher } from "svelte";
  export let items = [];
  const dispatch = createEventDispatcher();
  $: if (items.length) {
    localStorage.setItem("items", JSON.stringify(items));
  }
  const remove = item => {
    if (items.length === 1) {
      localStorage.removeItem("items");
    }
    dispatch("remove", {
      item
    });
  };
</script>

<style>
  .items {
    display: grid;
    grid-template: 1fr / 1fr;
    width: 80%;
    margin: 0 auto;
  }

  .item {
    padding: 10px;
    box-sizing: border-box;
    margin: 10px;
    box-shadow: 0 0 10px #ccc;
    border-radius: 5px;
    cursor: pointer;
    text-align: left;
    position: relative;
  }
</style>

<h2>Items: {items.length}</h2>
<h4>Click item to remove</h4>
<div class="items">
  {#each items as item}
    <div class="item" on:click={() => remove(item)}> {item} </div>
  {/each}
</div>
{#if !items.length}
  <h1 style="text-align: center">No Items</h1>
{/if}
