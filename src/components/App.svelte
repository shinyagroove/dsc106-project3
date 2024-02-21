<script>
    import * as d3 from 'd3';
    import Line from './line.svelte';
    import {onMount} from 'svelte';


    let data;
    let readme;
    let items;
    // fetch faker_grouped and faker_total onmount, from static folder
    onMount(async () => {
        const res = await fetch('data.json');
        const read = await fetch('readme.txt');
        data = await res.json();
        readme = await read.text();
        items = readme.split('\n\n');
    });


</script>

<main>
    <h1>An Analysis of Faker's Champion Pool Across Game Lengths</h1>
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
    <div class = "description">
        <p class = "para">
            We decided to encode the lengths of game time (which were aggregated after rounding to the closest minute) 
            on the X-axis and the average gold earned for a specific game time on the Y-axis. We also added lines connecting 
            the data points of each champion, and encoded distinctly different colors on these lines in order to represent 
            different champions. 
        </p>
        
        <p class = "para">
            These design choices were made so that the viewer could easily identify the relationship between the length of 
            the game and the gold earned. The color of each line stands out from the background, which was selected to be 
            a slightly off-white color to be a bit easier on the eyes as the viewer examines the graph. The encoding of a
            line allows the user to visually follow the gold earned as the game length increases, recognizing the differences
            (or lack thereof) between the slopes connecting two data points. Additionally, choosing distinct colors for 
            the lines allows for comparisons of different lines against each other to be easier. 
        </p>
        <p class = "para">
            We decided to allow users to click on the boxes in the legend to toggle the visibility of each line, and we 
            also added a button to select or deselect all of the lines in order to make it easier for the viewer to view 
            the relationship between a few lines specifically (since they are all selected by default). Allowing the user
            to select and deselect specific lines allows them to choose which lines they wish to compare and analyze. 
        </p>
        <p class = "para">
            We also allowed the user to hover over a specific point on the graph in order to bring up the champion, gold,
            and game length represented by that point. This allows them to easily perceive specific gold values and times 
            without having to try and analyze specifically where the values on the graph line up. 
        </p>
        <p class = "para">
            As for our individual contributions, Aneesh imported the data and cleaned it so that it would be fit to export 
            to our webpage. He also worked on making the legend interactive, as well as typing the writeup of the project. 
            Qilong created the basic line graph, implemented the interactivity of lines, and fixed some functional bugs.
        </p>
    </div>
</main>

<style>
@import url('https://fonts.googleapis.com/css2?family=Nunito:wght@300;400;700&display=swap');

:root {
    --color-bg: #f2eee2;
    --color-outline: #c2c2c2;
    --color-shadow: hsl(0, 0%, 0%, 0.1);
    --color-text: #3f4252;
    --color-bg-1: hsla(0, 0%, 0%, 0.2);
    --color-shadow-1: hsl(0, 0%, 96%);
    user-select: none;
}
:global(body) {
        background-color: #f7f5ed;
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

.para {
    padding: 10px;
}
</style>
