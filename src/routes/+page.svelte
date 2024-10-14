<script>
    import { browser } from '$app/environment';
    import { writable } from 'svelte/store';
	import { onMount, onDestroy } from 'svelte';
    import { HubConnectionBuilder } from '@microsoft/signalr';


    const host = import.meta.env.SIGNALR_HOST;
    const handshake = import.meta.env.HANDSHAKE;

    let messages = [];
    let connection;


    onMount(async () => {

    connection = new HubConnectionBuilder()
      .withUrl(host)
      .build();

    // Start the connection
    try {
      await connection.start();
      console.log('Connected to SignalR hub');
    } catch (err) {
      console.error('Error connecting to SignalR:', err);
    }

    // Listen for messages from the server
    connection.on('ReceiveMessage', (message) => {
      messages = [...messages, message];
    });

    // Optional: Handle connection closed
    connection.onclose(() => {
      console.log('Connection closed');
    });
  });

  // Clean up the connection when the component is destroyed
  const disconnect = async () => {
    if (connection) {
      await connection.stop();
      console.log('Disconnected from SignalR hub');
    }
  };

  // Use the onDestroy lifecycle method to clean up
  onDestroy(() => {
    disconnect();
  });
</script>

{#each messages as message, i}
	<p>{message}</p>
{/each}
<h1>Welcome to SvelteKit</h1>
<p>Visit <a href="https://kit.svelte.dev">kit.svelte.dev</a> to read the documentation</p>
