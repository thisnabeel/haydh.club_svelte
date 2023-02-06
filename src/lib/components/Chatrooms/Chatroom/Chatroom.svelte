
<script>
        import {onMount} from "svelte";
    import Messages from "../../Messages/Messages.svelte"

    export let chatroomId;
    const apiUrl = "http://localhost:3000/"
    let contentInput = "";
    let messages = []
    let period = false;

    onMount(async () => {
        let response = await fetch(`${apiUrl}/chat_rooms/${chatroomId}`);
        let chatroom = await response.json(); // read response body and parse as JSON
        console.log(chatroom)
        messages = chatroom.messages
        createChatRoomWebsocketConnection(chatroomId);
    })

    // Opens a WebSocket connection to a specific Chat Room stream.
function createChatRoomWebsocketConnection(chatroomId) {
    
    // Creates the new WebSocket connection.
    let socket = new WebSocket(`ws://localhost:3000/cable`);
     // When the connection is first created, this code runs subscribing the client to a specific chatroom stream in the ChatRoomChannel.
    socket.onopen = function(event) {
        console.log('WebSocket is connected.');
        const msg = {
            command: 'subscribe',
            identifier: JSON.stringify({
                id: chatroomId,
                channel: 'ChatRoomChannel'
            }),
        };
        socket.send(JSON.stringify(msg));
    };
    
    // When the connection is closed, this code will run.
    socket.onclose = function(event) {
         console.log('WebSocket is closed.');
    };
    // When a message is received through the websocket, this code will run.
    socket.onmessage = function(event) {            
        const response = event.data;
        const msg = JSON.parse(response);
        
        // Ignores pings.
        if (msg.type === "ping") {
            return;
        }
        console.log("FROM RAILS: ", msg);
        
        // Renders any newly created messages onto the page.
        if (msg.message) {
            // renderMessage(msg.message)
            console.log(msg.message)
            messages = [...messages, msg.message]
        }
        
    };
    
    // When an error occurs through the websocket connection, this code is run printing the error message.
    socket.onerror = function(error) {
        console.log('WebSocket Error: ' + error);
    };
}

    // newMessageForm.addEventListener('submit', event => {
    //     event.preventDefault();
    //     fetch(`${apiUrl}/messages`,{
    //         method: 'POST',
    //         headers: {
    //             'Content-Type': 'application/json',
    //             'Accept': 'application/json'
    //         },
    //         body: JSON.stringify({
    //             content: event.target[0].value,
    //             chat_room_id: event.target.dataset.chatroomId
    //         })
    //     })
    //     // .then(response => response.json())
    //     // .then(messageObject => {renderMessage(messageObject)})
    //     newMessageForm.reset();
    // })
    
    let submitMessage = () => {
        console.log(contentInput)
        fetch(`${apiUrl}/messages`,{
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
                'Accept': 'application/json'
            },
            body: JSON.stringify({
                content: contentInput,
                chat_room_id: chatroomId
            })
        })
        .then(response => response.json())
        .then(messageObject => {
            messages = [...messages, messageObject];
        })
        contentInput = "";
    }
</script>

    <input type="text" class="form-control" bind:value={contentInput}>
    <button class="btn btn-info" on:click={submitMessage}>Submit</button>
    <hr>
    
    <Messages {messages}></Messages>