<script>
    import * as d3 from 'd3';
    import Line from './line.svelte';
    import {onMount} from 'svelte';


    let data;

    // fetch faker_grouped and faker_total onmount, from static folder
    onMount(async () => {
        const res = await fetch('data.json');
        data = await res.json();
    });


</script>

<main>
    <h1>Line Graph</h1>
    <!-- pass faker_grouped and faker_total into Line component-->
    {#if data}
        <Line {data} />
    {/if}

    <div class="description">
            <p>
                Our project showcases the relationship between 
                the length of game and the amount of gold earned 
                for various champions played by professional League 
                of Legends player Lee Sang-hyeok, better known as Faker. 
                The data was collected from the website Oracle's Elixir, 
                and the data for professional games played by Faker between 
                2020 and 2023 (inclusive) was taken and aggregated by 
                champion played and game length. Some data, such as 
                champions that were played in very few games, were removed 
                in order to allow the data to provide a more cohesive 
                representation of the relationship between the variables.
            </p>
    </div>

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
    user-select: none;
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

.description {
    margin: 0 auto;
    max-width: 800px;
    text-align: left;
    padding: 20px;
    border-radius: 10px;
    box-shadow: 0 0 10px 0 var(--color-shadow-1);
    background-color: var(--color-bg-1);
    margin-top: 20px;
    font-size: 15px;
}

</style>
