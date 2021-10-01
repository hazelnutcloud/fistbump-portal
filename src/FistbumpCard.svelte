<script>
    export let fistbump;
</script>

<div class="container">
    <p><span class="bold">Sender:</span> {fistbump.sender}</p>
    <p><span class="bold">Message:</span> {fistbump.message}</p>
    <p><span class="bold">Time:</span> {new Date(fistbump.timestamp * 1000)}</p>
</div>

<style>
    .bold {
        font-weight: 700;
    }
    p {
        color: #F1F1F1;
    }
    .container {
        text-align: left;
        padding: 0.2em 1em;
        border-bottom: 1px solid black;
    }
</style>