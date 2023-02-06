<script>
    import {onMount} from "svelte";
    import {user} from "$lib/stores/user";
    import {API_URL} from "$lib/Env"

    let user_signed_in;
    let socket;
    user.subscribe(value => {
        user_signed_in = value
        if (user_signed_in.period === false) {
            console.log("CLOSING PERIOD")
            socket.close();
        }
    })

    let users = [user_signed_in];
    const apiUrl = API_URL

    // $: user_signed_in && closeConnection()

    // async function closeConnection() {
    //     console.log("CLOSING PERIOD", user_signed_in)
    //     if (socket && user_signed_in.period === false) {
    //         console.log("CLOSING PERIOD")
    //         socket.close()
    //     }
    // }

    onMount(async () => {
        let response = await fetch(`${apiUrl}/companions.json`);
        let list = await response.json(); // read response body and parse as JSON
        users = list
    })

    createCompanionsWebsocketConnection()

   // Opens a WebSocket connection to a specific Chat Room stream.
function createCompanionsWebsocketConnection() {
    
    // Creates the new WebSocket connection.
    socket = new WebSocket(`ws://${apiUrl.split("//")[1]}/cable`);
     // When the connection is first created, this code runs subscribing the client to a specific companions stream in the CompanionsChannel.
    socket.onopen = function(event) {
        console.log('COMPANIONS CHANNEL WebSocket is connected.');
        const msg = {
            command: 'subscribe',
            identifier: JSON.stringify({
                channel: 'CompanionsChannel'
            }),
        };
        socket.send(JSON.stringify(msg));
        console.log(JSON.stringify(msg))
    };
    
    // When the connection is closed, this code will run.
    socket.onclose = function(event) {
         console.log('WebSocket is closed.');
    };
    // When a message is received through the websocket, this code will run.
    socket.onmessage = function(event) {      
     
        const response = event.data;
        const data = JSON.parse(response);
        
        // Ignores pings.
        if (data.type === "ping") {
            return;
        }
        console.log("COMPANIONS MESSAGE: ", data);
        
        const res = data.message
        // Renders any newly created messages onto the page.
        if (res.joined) {
            // renderMessage(msg.user)
            console.log("JOOIIINed")
            users = [...users, res.joined]
        }

        if (res.left) {
            // renderMessage(msg.user)
            users = users.filter(companion => companion.id !== res.left.id)
        }
        
    };
    
    // When an error occurs through the websocket connection, this code is run printing the error message.
    socket.onerror = function(error) {
        console.log('WebSocket Error: ' + error);
    };
}
</script>

<h1>Crewmates: </h1>

{#each users as user}
    <li>
        <div class="header">
            {user.first_name + " " + user.last_name}
        </div>
    </li>
{/each}

<hr>

<style>
    li {
        list-style: none;
    }
    .header {
        padding: 10px;
        color: #5D2A42;
        background: #fff;
        border: 1px solid #FFDCCC;
        font-size: 24px;
    }
</style>
