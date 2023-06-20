<script>
    export let to;
    export let direction;

    const angle = {
        top: 0,
        right: 90,
        bottom: 180,
        left: 270,
    }[to];
</script>

<!-- svelte-ignore a11y-click-events-have-key-events -->
<div style="transform: rotate({angle}deg);" on:click={() => (direction = to)}>
    ⇧
</div>
