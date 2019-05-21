<script>
  import { onMount } from "svelte";
  import { fade } from "svelte/transition";
  import Notifications from "./components/Notifications.svelte";
  import Items from "./components/Items.svelte";
  let username = "";
  let currentUsername = localStorage.getItem("username");
  let text = "";
  let items = new Set();
  let notifications = [];
  let loading = false;
  let currentDay = new Date().toString();
  let notificationComponent;
  let addNotification;

  const sleep = timeout => new Promise(resolve => setTimeout(resolve, timeout));
  const addItem = () => {
    try {
      if (!text) return;
      if (items.has(text)) throw new Error("Item already added");
      items.add(text);
      items = new Set([...items]);
      addNotification({
        message: `Added item: ${text}`
      });
    } catch (error) {
      addNotification({
        message: error.message,
        type: "error"
      });
    } finally {
      text = "";
    }
  };
  const deleteItem = item => {
    console.log(item);
    items.delete(item);
    items = new Set([...items]);
  };
  const handleKeyPress = event => {
    const actions = {
      Enter: addItem
    };
    const action = actions[event.key];
    if (typeof action !== "function") return;
    action();
  };

  const sendItems = async () => {
    if (loading) return;
    try {
      loading = true;
      throw new Error(+new Date() + Math.random(0, +new Date()));
    } catch (e) {
      const error = {
        message: e.message,
        type: "error"
      };
      addNotification(error);
    } finally {
      loading = false;
    }
  };

  onMount(() => {
    addNotification = notificationComponent.enqueue;
    try {
      const itemString = localStorage.getItem("items");
      items = new Set(JSON.parse(itemString));
      console.log(items);
    } catch (error) {
      items = new Set();
    }
  });

  const removeItem = ({ detail }) => deleteItem(detail.item);
  const setUsername = () => {
    currentUsername = username;
    localStorage.setItem("username", currentUsername);
  };
  const setUsernameEnter = event => {
    console.log(event);
    if (event.key !== "Enter") return;
    setUsername();
  };
  const logout = () => {
    currentUsername = username = "";
    localStorage.removeItem("username");
  };
</script>

<style>
  .input-container {
    margin: 10px auto;
    width: 80%;
  }
  .input {
    padding: 15px 10px;
    outline: 0;
    border-radius: 5px;
    border: 1px solid #222;
    display: block;
    width: 100%;
  }
  .button {
    border-radius: 5px;
    border: 1px solid #111;
    background: #222;
    color: #fff;
    cursor: pointer;
    padding: 10px 5px;
    width: 300px;
    margin: 0 auto;
    display: block;
    font-size: 20px;
    font-weight: bold;
    text-transform: uppercase;
    transition: 0.3s ease-in-out;
  }
  .button:hover {
    border-color: #33dd00;
  }
  main {
    margin-top: 100px;
    text-align: center;
  }
  .text-center {
    text-align: center;
  }
</style>

<main>
  {#if currentUsername}
    <div class="text-center">
      <h1>Hello, {currentUsername}</h1>
      <button
        class="button"
        style="font-size: 16px; width: 100px; padding: 5px 2.5px;"
        on:click={logout}>
        Logout
      </button>
    </div>
    <div class="input-container">
      {#if text}
        <label transition:fade>
          What did you do today? [Press Enter to Add]
        </label>
      {/if}
      <input
        class="input"
        bind:value={text}
        type="text"
        placeholder="What did you do today? [Press Enter to Add]"
        on:keypress={handleKeyPress} />
      <button class="button" on:click={addItem}>Add</button>
    </div>
    <div class="text-center">
      <h2>Things you've done today</h2>
      <h3>{currentDay}</h3>
    </div>
    {#if items.length}
      <button on:click={sendItems} disabled={loading}>Send</button>
    {/if}
    <Items items={Array.from(items)} on:remove={removeItem} />
  {:else}
    <h1>Enter your username.</h1>
    <input
      class="input"
      bind:value={username}
      placeholder="Enter your username"
      on:keypress={setUsernameEnter} />
    <button class="button" on:click={setUsername}>Login</button>
  {/if}
</main>

<Notifications {notifications} bind:this={notificationComponent} />
