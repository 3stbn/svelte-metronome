<script>
    import { bpm } from "./stores.js";
    export let handleTempoChange;

    export let mode = "increase";
    export let minBpm;
    export let maxBpm;

    function handleChange() {
        if ($bpm < maxBpm && mode === "increase") {
            $bpm = $bpm + 1;

            handleTempoChange();
        }

        if ($bpm > minBpm && mode === "decrease") {
            $bpm = $bpm - 1;
            handleTempoChange();
        }
    }
</script>

<style>
    button {
        background: #f83d02;
        height: 30px;
        width: 30px;
        border: none;
        color: #fff;
        font-size: 10px;
        font-weight: bolder;
        border-radius: 20%;
    }
</style>

<button on:click={handleChange}>{mode === 'increase' ? '+' : '-'}</button>
