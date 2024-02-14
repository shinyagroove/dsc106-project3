<script>
    import * as d3 from 'd3';
    import Line from './line.svelte';
    import {onMount} from 'svelte';


    let faker_grouped;
    let faker_total;

    // fetch faker_grouped and faker_total onmount, from static folder
    onMount(async () => {
        const res = await fetch('faker_grouped.json');
        faker_grouped = await res.json();

        const res2 = await fetch('faker_total.json');
        faker_total = await res2.json();
        
    });


</script>

<main>
    <h1>Line Graph</h1>
    <!-- pass faker_grouped and faker_total into Line component-->
    {#if faker_grouped && faker_total}
        <Line {faker_grouped} {faker_total} />
    {/if}
</main>

<style>
@import url('https://fonts.googleapis.com/css2?family=Nunito:wght@300;400;700&display=swap');

:root {
    --color-bg: #ffffff;
    --color-outline: #c2c2c2;
    --color-shadow: hsl(0, 0%, 0%, 0.1);
    --color-text: #3f4252;
    --color-bg-1: hsla(0, 0%, 0%, 0.2);
    --color-shadow-1: hsl(0, 0%, 96%);
}

*,
*::before,
*::after {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

main {
    text-align: center;
    font-family: 'Nunito', sans-serif;
    font-weight: 300;
    line-height: 2;
    font-size: 24px;
    color: var(--color-text);
}

h1 {
    font-size: 2em;
    font-weight: 300;
    line-height: 2;
}
</style>
