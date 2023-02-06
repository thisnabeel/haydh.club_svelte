<script>

    import {onMount} from "svelte";
    import Chatroom from "../lib/components/Chatrooms/Chatroom/Chatroom.svelte";
    import Companions from "../lib/components/Companions/Companions.svelte";
    import LogIn from "$lib/components/LogIn/LogIn.svelte";
    import PeriodToggle from "$lib/components/Period/Toggle.svelte"

    let period = false;

    import {user} from "$lib/stores/user";

    let user_signed_in;
    user.subscribe(value => {
        user_signed_in = value
        if (user_signed_in) {
            console.log("HIIII", user_signed_in)
            period = user_signed_in.period
            console.log(period)
        }
    })

   
</script>

<div class="container">
    <div class="text-center">
        <br>
        <h1>Haydh.Club</h1>
        <PeriodToggle></PeriodToggle>
    </div>
    <p></p>
    

    {#if user_signed_in}

        {#if period}
            <Companions></Companions>
            <Chatroom chatroomId={1}></Chatroom>
        {/if}
    {:else}
        <LogIn></LogIn>
    {/if}


    <br>


</div>    

