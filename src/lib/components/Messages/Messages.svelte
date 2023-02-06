<script>
    export let messages;
    $: console.log("messages", messages)
</script>

<ul>
    {#each messages || [] as message}
        <li>{message.content}</li>
    {/each}
</ul>