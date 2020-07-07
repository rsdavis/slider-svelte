<script>

    import { onMount } from 'svelte'

    let min = 0;
    let max = 100;
    let options = [];
    let dragging = false;
    let mouseStart = 0;
    let wrapRef = null
    let ulRef = null;
    let trans = 0;
    let selected = 0;
    let item_width = 0.1;
    let gap_width = 0.05;
    for (let i = min; i < max; i++) options.push(i);


    function calcSelected (pos) {
        let total_px = wrapRef.getBoundingClientRect().width
        let item_px = total_px * item_width
        let gap_px = total_px * gap_width
        let midpoint_px = total_px*0.5
        selected = Math.floor((midpoint_px - pos) / (item_px + gap_px))
    }

    function snap() {
        let total_px = wrapRef.getBoundingClientRect().width
        let item_px = total_px * item_width
        let gap_px = total_px * gap_width
        let midpoint_px = total_px*0.5
        selected = Math.floor((midpoint_px - trans) / (item_px + gap_px))
        let pos = selected * (item_px + gap_px) + 0.5 * (item_px + gap_px) + trans
        let snap = midpoint_px - pos
        trans += snap
        ulRef.style.setProperty('transform', 'translate(' + trans.toString() + 'px)')
        ulRef.style.setProperty('transition', '0.1s ease')
    }

    function dragStart(event) {
        dragging = true;
        mouseStart = event.clientX;
        ulRef.style.setProperty('transition', 'none')
    }

    function dragSlide(event) {
        if (dragging) {
            let x = event.clientX;
            let diff = x - mouseStart;
            let pos = trans + diff;
            ulRef.style.setProperty(
                "transform",
                "translate(" + pos.toString() + "px)"
            )
            calcSelected(pos)
        }
    }

    function dragStop(event) {
        if (dragging) {
            let mouseEnd = event.clientX
            trans = trans + mouseEnd - mouseStart
            dragging = false
            snap()
        }
    }

    onMount(snap)

</script>

<style>

    .container {
        display: flex;
        align-items: center;
        position: relative;
    }

    .line {
        position: fixed;
        height: 100px;
        left: 50%;
        width: 1px;
        border: 1px solid green;
    }

    .arrow {
        width: 15%;
    }

    .wrap {
        overflow: hidden;
        border: 0px solid blue;
        height: 50px;

    }

    ul {
        list-style: none;
        padding: 0;
        margin: 0;
        flex-grow: 1;
        white-space: nowrap;
        overflow: visible;
        height: 100%;
    }

    li {
        display: inline-block;
        width: var(--item-width);
        height: 100%;
        margin: 0 calc(0.5 * var(--gap-width));
    }

    li + li {
    }

    button {
        width: 100%;
        margin: 0;
        text-align: center;
    }
</style>

<div class="container" style="--item-width: {item_width*100}%; --gap-width: {gap_width*100}%">

    <div class='line'></div>
    <div class="left arrow">L</div>

    <div
        class="wrap"
        bind:this={wrapRef}
        on:mousedown={dragStart}
        on:mousemove={dragSlide}
        on:mouseup={dragStop}
        on:mouseleave={dragStop}
    >
        <ul bind:this={ulRef}>
            {#each options as option}
                <li>
                    <button>{option}</button>
                </li>
            {/each}
        </ul>
    </div>
    <div class="right arrow">R</div>

</div>

<p>Selected: {selected}</p>