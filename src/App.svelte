<script>
    import click1Audio from "./assets/click1.wav";
    import click2Audio from "./assets/click2.wav";
    import ChangeTempo from "./ChangeTempo.svelte";
    import Dot from "./Dot.svelte";
    import { tempoMarkings } from "./utils.js";
    import { bpm } from "./stores.js";
    import { onDestroy } from "svelte";

    let playing = false;
    let beatsPerMeasure = 4;
    let beat = 0;
    let minBpm = 40;
    let maxBpm = 230;

    let click1 = new Audio(click1Audio);
    let click2 = new Audio(click2Audio);

    let timer;

    onDestroy(() => {
        if (timer) {
            clearInterval(timer);
        }
    });

    $: bpmInterval = (60 / $bpm) * 1000;
    $: tempoMarking = tempoMarkings.find(marking => $bpm >= marking.min && $bpm <= marking.max).name;
    $: dots = [...Array(beatsPerMeasure).keys()]; // [ 0, 1, 2, 3]

    function startStop() {
        if (playing) {
            clearInterval(timer);
            playing = false;
            beat = 0;
        } else {
            timer = setInterval(playClick, bpmInterval);
            playing = true;
            beat = 0;
            playClick();
        }
    }

    function playClick() {
        if (beat % beatsPerMeasure === 0) {
            click2.play();
        } else {
            click1.play();
        }

        beat = (beat + 1) % beatsPerMeasure;
    }

    function handleTempoChange() {
        if (playing) {
            clearInterval(timer);
            timer = setInterval(playClick, bpmInterval);
            playClick();
            beat = 0;
        }
    }
</script>

<style>
    h1 {
        color: #f83d02;
        font-size: 40px;
    }
    main {
        text-align: center;
        max-width: 375px;
        margin: 0 auto;
        padding: 30px;
    }
    section {
        display: flex;
        align-items: center;
    }

    .slider {
        width: 100%;
        margin: 30px 10px;
    }

    .play {
        background: #f83d02;
        height: 100px;
        width: 100px;
        border: none;
        border-radius: 100%;
        color: #fff;
        font-size: 28px;
    }
    .slider {
        -webkit-appearance: none;
        width: 100%;
        height: 15px;
        border-radius: 5px;
        background: #d3d3d3;
        outline: none;
        opacity: 0.7;
        -webkit-transition: 0.2s;
        transition: opacity 0.2s;
    }

    .slider:hover {
        opacity: 1;
    }

    .slider::-webkit-slider-thumb {
        -webkit-appearance: none;
        appearance: none;
        width: 25px;
        height: 25px;
        border-radius: 50%;
        background: #f83d02;
        cursor: pointer;
    }

    .slider::-moz-range-thumb {
        width: 25px;
        height: 25px;
        border-radius: 50%;
        background: #f83d02;
        cursor: pointer;
    }
    .dots {
        display: flex;
        justify-content: center;
    }
</style>

<main>
    <h1>{$bpm} BPM</h1>
    <h3>{tempoMarking}</h3>
    <label>Beats per measure: {beatsPerMeasure}</label>
    <input type="range" min="1" max="10" bind:value={beatsPerMeasure} on:change={handleTempoChange} class="slider" />
    <div class="dots">
        {#each dots as dot}
            <Dot active={beat === dot + 1 || (beat === 0 && dots.length - 1 === dot && playing)} {bpmInterval} />
        {/each}
    </div>
    <section>
        <ChangeTempo {handleTempoChange} mode="decrease" {minBpm} {maxBpm} />
        <input type="range" min={minBpm} max={maxBpm} bind:value={$bpm} on:change={handleTempoChange} class="slider" />
        <ChangeTempo {handleTempoChange} {minBpm} {maxBpm} />
    </section>

    <button class="play" on:click={startStop}>{playing ? '■' : '▶'}</button>
</main>
