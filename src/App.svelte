<script>
    import Slider from './Slider.svelte'

    let selected = 0

</script>

<div class='container'>
    <Slider bind:value={selected} min={5} max={13}/>
</div>

<div class='container'>
    <p>Selected: {selected}</p>
</div>

<style>

    .container {
        width: 80%;
        margin: 50px auto;
        border: 1px solid red;
    }

</style>