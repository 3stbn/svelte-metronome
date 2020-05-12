<script>
    import { scale } from "svelte/transition";
    export let active = false;
    export let bpmInterval;
</script>

<style>
    div {
        width: 25px;
        height: 25px;
        border: 1px solid #f83d02;
        border-radius: 50%;
    }
    .active {
        background: #f83d02;
    }
</style>

{#if active}
    <div class="active" in:scale={{ duration: bpmInterval }} />
{:else}
    <div />
{/if}
