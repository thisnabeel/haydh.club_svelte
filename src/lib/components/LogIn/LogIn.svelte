
<script>
    import Api from "$lib/api/api";
    import { goto } from '$app/navigation';
    import { user } from "$lib/stores/user"

    let email = "";
    let password = "";
    let reconfirmPassword = "";
    let signingUp = false;

    let first_name = "";
    let last_name = "";

    $: console.log(email);

    const authenticate = async () => {

        let required = [email, password]
        if (required.includes("")){
            alert("Please complete form");
            return;
        }

        const response = await Api.post("/users/sign_in.json", {
            email: email,
            password: password
        });
        console.log(response)
        if (response["id"]) {
            user.set(response);
            // goto(`/stories`);
        }
    }

    async function submitRegistration() {

        let required = [email, password, reconfirmPassword]
        if (required.includes("")){
            alert("Error: Please complete form");
            return;
        }

        if (password != reconfirmPassword){
            alert("Error: Passwords don't match.");
            return;
        }

        const response = await Api.post("/users.json", {
            user: {
                email: email,
                password: password,
                password_confirmation: reconfirmPassword,
                first_name: first_name,
                last_name: last_name
            },
            method: "_post"
        });
        console.log(response)
        // if (response["id"]) {
        //     user.set(response);
        // }
    }
    
</script>

    <div class="form">
        <img src="/sign-in.png" alt="" class="sign-up-img creds-header-img">
        
        <label>Email:</label>
        <input type="text" bind:value={email}>

        {#if signingUp}
        <div class="row">

            <div class="col-lg-6 col-md-6 col-sm-6">
                <label>First Name:</label>
                <input type="text" bind:value={first_name}>

            </div>
            <div class="col-lg-6 col-md-6 col-sm-6">
                <label>Last Name:</label>
                <input type="text" bind:value={last_name}>
            </div>
        </div>
        {/if}

        <label>Password:</label>
        <input type="password" bind:value={password}>

        {#if signingUp}
            <label>Re-confirm Password:</label>
            <input type="password" bind:value={reconfirmPassword}>

            <button class="signUp" on:click={submitRegistration}>Finish Signing Up</button>
            <button class="signUp" on:click={() => {
                signingUp = false;
            }}>Log In</button>
        {:else}
            <button class="signIn" on:click={authenticate}>Log In</button>
            <button class="signUp" on:click={() => signingUp = true}>Sign Up</button>
        {/if}


    </div>

  <style>
    .form {
        max-width: 400px;
        margin: 30px auto;
        background: #fff;
        padding: 30px;
        border-radius: 6px;
        border: 9px solid #f6f8ff;
    }

    img.creds-header-img {
        margin: 10px auto;
        display: block;
        width: 100%;
    }

    input {
        width: 100%;
        color: rgb(49, 49, 49);
        border: solid 1px #ccc;
    }

    .signIn, .signUp {
        margin-top: 10px;
        background-color: #FFFE8B;
        display: block;
        width: 100%;
        height: calc(1.5em + 0.75rem + 2px);
        padding: 0.375rem 0.75rem;
        font-size: 1rem;
        font-weight: 400;
        line-height: 1.5;
        color: #495057;
        background-clip: padding-box;
        border: 1px solid #ced4da;
        border-radius: 0.25rem;
        transition: border-color 0.15s ease-in-out, box-shadow 0.15s ease-in-out, background-color 0.35s ease-in-out, color 0.35s ease-in-out;
    }

    .signUp {
        margin-top: 4px;
        background-color: transparent;
        border-color: transparent;
    }

    .signUp:hover {
        background-color: rgb(204, 0, 255);
        border: 1px solid #ced4da;
        color: #fff;
    }
  </style>