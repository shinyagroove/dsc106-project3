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
        .x((d) => x(d.gamelength)-marginLeft).y((d) => y(d.earnedgold));

    // group data by champion
    $: data = d3.group(faker_grouped, (d) => d.champion);

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
</script>

<main>
    <div class="line-plot">
        <svg
            bind:this={svg}
            width = "1500"
            {height}
            viewBox="0 0 {width} {height}"
            style="max-width: 100%; height: auto;"
        >

            <!-- x-axis -->
            <g bind:this={gx} transform="translate(0,{height - marginBottom})" />
            
            <!-- y-axis -->
            <g bind:this={gy} transform="translate({marginLeft},0)">
                <text 
                    x="5"
                    y="{marginTop}"
                    font-weight="bold"
                    fill="black"
                    text-anchor="start"
                >
                    Earned Gold
                </text>

            <!-- lines faker_grouped -->
            <g stroke-opacity=".75">
                {#each data as d, i}
                    <path
                        key={i}
                        d={line(d[1])}
                        stroke={color(i)}
                        stroke-width="2"
                    />
                {/each}
            </g>

            <!-- line faker_total -->
            <path
                d={line(faker_total)}
                stroke="black"
                stroke-width="2"
            />

            <!-- legend -->
            <g bind:this={legend} transform="translate({width - marginRight}, 0)">

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
                            fill={color(i)}
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
                        fill="black"
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


            </g>

            

        </svg>
    </div>
    
</main>