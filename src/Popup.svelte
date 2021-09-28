<script>
    import { fly, fade } from "svelte/transition";
    import { quintIn } from "svelte/easing";

    export let handleClick;
</script>

<div class="blur" on:click={handleClick} />
<div out:fade class="success-card">
    
    <span class="success" in:fly={{ x: -50, duration: 1000, easing: quintIn }}
        >🤜</span
    ><span class="success" in:fly={{ x: 50, duration: 1000, easing: quintIn }}
        >🤛</span
    ><br /><span in:fade={{ duration: 1500, easing: quintIn }}
        >fistbump successful!</span
    > <br>
    <button on:click={handleClick}> 👌 cool </button>
    
</div>

<style>
    .success {
        display: inline-block;
        color: linear-gradient(
            to right,
            orange,
            yellow,
            green,
            cyan,
            blue,
            violet
        );
        font-size: 3em;
    }
    .success-card {
        border: 1px red solid;
        padding: 0 1em 1em;
        /* From https://css.glass */
        background: rgba(255, 255, 255, 0.5);
        border-radius: 16px;
        box-shadow: 0 4px 30px rgba(0, 0, 0, 0.1);
        backdrop-filter: blur(5px);
        -webkit-backdrop-filter: blur(5px);
        border: 1px solid rgba(255, 255, 255, 0.3);
        display: inline-block;
        z-index: 999;
        position: fixed;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        width: 100%;
        max-width: 400px;
        text-align: center;
        font-weight: 700;
    }
    .blur {
        position: fixed;
        top: 0;
        left: 0;
        width: 100vw;
        height: 100vh;
        background: black;
        opacity: 0.5;
        filter: blur(5px);
        z-index: 0;
    }
    button {
        margin-top: 1em;
        border-radius: 6px;
        padding: 0.7em 1em;
    }
</style>
