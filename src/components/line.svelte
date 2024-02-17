<script>
    import * as d3 from 'd3';

    export let faker_grouped;
    export let faker_total;


    const width = 928;
    const height = 600;
    const marginTop = 20;
    const marginRight = 30;
    const marginBottom = 30;
    const marginLeft = 40;

    let svg;
    let legend;

    // Placeholders for the axis elements.
    let gx;
    let gy;

    let data;
    let checked_champs = {};
    let overall = true;

    // map gamelength to x-coordinate on screen
    $: x = d3
        .scaleLinear()
        .domain(d3.extent(faker_grouped, (d) => d.gamelength))
        .range([marginLeft, width - marginRight]);

    // map earnedgold to y-coordinate on screen
    $: y = d3
        .scaleLinear()
        .domain([0, 14000])
        .range([height - marginBottom, marginTop]);

    // make line
    $: line = d3
        .line()
        .x((d) => x(d.gamelength)).y((d) => y(d.earnedgold));

    // group data by champion
    data = d3.group(faker_grouped, (d) => d.champion);
    data.forEach(d => {
        checked_champs[d[0].champion] = true
    });

    // x-axis
    $: gx = d3
        .select(gx)
        .call(d3.axisBottom(x).ticks(width / 80));

    // y-axis
    $: gy = d3.select(gy)
        .call(d3.axisLeft(y).ticks(null))
        .call(
            (g) => g.selectAll('.tick line')
            .clone()
            .attr('x2', width - marginRight - marginLeft)
            .attr('stroke-opacity', (d) => (d === 0 ? 1 : 0.1)));

    // color line by champion
    $: color = d3.scaleOrdinal(d3.schemeTableau10);



    $: console.log(checked_champs);
    $: console.log(overall);

    function uncheck_all(data){
        uncheck_overall();
        data.forEach((d, i) => {
            uncheck_champ(d);
        });
        
        
    }
    function uncheck_overall(){
        overall = !overall;
    };
    
    function uncheck_champ(d){
        checked_champs[d[0]] = !checked_champs[d[0]];
    };



</script>

<main>
    <div class="line-plot">
            
        <svg
            bind:this={svg}
            {width}
            {height}
            viewBox="0 0 {width} {height}"
            style="max-width: 100%; height: auto;"
        >
        
            <!-- x-axis -->
            <g bind:this={gx} transform="translate(0,{height - marginBottom})" />
            
            <!-- y-axis -->
            <g bind:this={gy} transform="translate({marginLeft},0)">
                <text 
                    x="0"
                    y="{marginTop}"
                    font-weight="bold"
                    fill="black"
                    text-anchor="start"
                >
                    Earned Gold
                </text>
            </g>
            
            <!-- lines faker_grouped -->
            <g stroke-opacity=".75">
                {#each data as d, i}
                    <path
                        key={i}
                        d={line(d[1])}
                        stroke={color(i)}
                        stroke-width={checked_champs[d[0]] === true ? "2": "0"}
                        fill="none"
                    />
                {/each}
            </g>

            <!-- line faker_total -->
            <path
                d={line(faker_total)}
                stroke="black"
                stroke-width={overall === true ? "2": "0"}
                fill="none"
            />
            
        </svg>

        <svg
            bind:this={svg}
            width={200}
            {height}
            viewBox="0 0 {200} {height}"
            style="max-width: 100%; height: auto;"
        >
            <!-- legend -->
            <g bind:this={legend} transform="translate(10, 0)">
                <text
                    y={marginTop}
                    font-size="12"
                    font-weight="bold"
                    fill="black"
                    text-anchor="start"
                >
                    Champion
                </text>

                <!-- faker_grouped -->
                {#each data as d, i}
                    <g transform="translate(0, {40 + i * 20} )">
                        <rect
                            width="10"
                            height="10"
                            fill={checked_champs[d[0]] === true ? color(i): "white"}
                            stroke = "black"
                            id = "{d} rect" 
                            on:click = {uncheck_champ(d)}
                        />
                        <text
                            x="15"
                            y="10"
                            font-size="12"
                            font-weight="auto"
                            fill="black"
                            text-anchor="start"
                        >
                            {d[0]}
                        </text>
                    </g>
                {/each}

                <!-- faker_total -->
                <g transform="translate(0, {40 + data.size*20} )">
                    <rect
                        width="10"
                        height="10"
                        stroke = "black"
                        fill={overall === true ? "black": "white"}
                        id = "overall rect"
                        on:click = {uncheck_overall}
                    />
                    <text
                        x="15"
                        y="10"
                        font-size="12"
                        font-weight="auto"
                        fill="black"
                        text-anchor="start"
                    >
                        Overall
                    </text>
                </g>
                
                <g transform = "translate(5, {70 + data.size*20} )">
                    <circle
                        r="10" 
                        stroke = "black" 
                        fill={overall === true ? "lightblue": "white"}
                        on:click = {uncheck_all(data)}
                        >
                    </circle>
                    <text
                        x="15"
                        y="5"
                        font-size="12"
                        font-weight="auto"
                        fill="black"
                        text-anchor="start"
                    >
                        Select / Deselect All
                    </text>
                </g>

            </g>

            

        </svg>
    </div>
    
</main>

<style>

.line-plot {
    position: absolute;
    left: 280px;
}


</style>