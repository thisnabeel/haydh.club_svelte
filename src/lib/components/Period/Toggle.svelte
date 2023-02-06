<script>

    import {user} from "$lib/stores/user";
    import Api from "$lib/api/api";

    let current_user;
    let period = false;
    user.subscribe(value => {
        current_user = value;
        if (current_user) {

            period = current_user.period;

        }
    })

    async function togglePeriod() {
        if (period === true) {

            if (confirm("Are you sure you finished with your period?") == true) {
            } else {
               return;
            }
        }
        const data = await Api.post(`/toggle_period`, {
            user_id: current_user.id,
        });
        period = data.period;
        user.set(data)
    }
</script>
<i class="fa fa-5x" class:fa-toggle-off="{period === false}" class:fa-toggle-on="{period === true}" on:click={() => togglePeriod()}></i>

<style>
    .fa-toggle-off {
        color: green;
    }

    .fa-toggle-on {
        color: red;
    }
</style>