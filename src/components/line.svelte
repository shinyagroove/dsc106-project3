<script>
    import * as d3 from 'd3';

    export let data;



    const width = 928;
    const height = 600;
    const marginTop = 20;
    const marginRight = 30;
    const marginBottom = 30;
    const marginLeft = 40;

    let svg_line;
    let svg_legend;
    let lines;
    let dots;

    // Placeholders for the axis elements.
    let gx;
    let gy;

    let checked_champs = {};
    let select_deselect_all = true;

    // map gamelength to x-coordinate on screen
    $: x = d3
        .scaleLinear()
        .domain(d3.extent(data, (d) => d.gamelength))
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
    let grouped;
    grouped = d3.group(data, (d) => d.champion);
    grouped.forEach(d => {
        checked_champs[d[0].champion] = true;
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

    function uncheck_all(){
        if (select_deselect_all === true){
            for (let key in checked_champs){
                checked_champs[key] = false;
            }
            select_deselect_all = false;
        } else {
            for (let key in checked_champs){
                checked_champs[key] = true;
            }
            select_deselect_all = true;
        }
    }

    function uncheck_champ(d){
        checked_champs[d[0]] = !checked_champs[d[0]];
    };

    // interactive tooltip for line
    let tooltipPt = null;

    function onPointerMove(event) {
    }

    function onPointerEnter(event) {
        d3.select(lines).attr("stroke-opacity", ".2");
        d3.select(dots).attr("opacity", "1");
    }


    function onPointerLeave(event) {
        tooltipPt = null;
        d3.select(lines).attr("stroke-opacity", ".75");
        d3.select(dots).attr("opacity", "0");
    }
    
    $: d3.select(svg_line)
        .on('pointermove', onPointerMove)
        .on('pointerenter', onPointerEnter)
        .on('pointerleave', onPointerLeave);

    console.log(grouped)
</script>

<main>
    <div class="line-plot">
            
        <svg
            bind:this={svg_line}
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
            
            <!-- lines -->
            <g bind:this={lines} stroke-opacity=".75">
                {#each grouped as d, i}
                    <path
                        key={i}
                        d={line(d[1])}
                        stroke={d[0] === "Overall" ? "black" : color(i)}
                        stroke-width={checked_champs[d[0]] === true ? "2": "0"}
                        fill="none"
                    />
                    {console.log(d[0])}
                {/each}
            </g>
            
            <!-- dots-->
            <g bind:this={dots} stroke="#000" stroke-opacity="0.2">
                {#each data as d, i}
                    <circle
                        key={i}
                        cx={x(d.gamelength)}
                        cy={y(d.earnedgold)}
                        r="2.5"
                        fill={color(i)}
                        opacity = 0
                    />
                {/each}
            </g>
            
        </svg>

        <svg
            bind:this={svg_legend}
            width={200}
            {height}
            viewBox="0 0 {200} {height}"
            style="max-width: 100%; height: auto;"
        >
            <!-- legend -->
            <g transform="translate(10, 0)">
                <text
                    y={marginTop}
                    font-size="12"
                    font-weight="bold"
                    fill="black"
                    text-anchor="start"
                >
                    Champion
                </text>

                {#each grouped as d, i}
                    <g transform="translate(0, {40 + i * 20} )">
                        <rect
                            width="10"
                            height="10"
                            fill={d[0] === "Overall" && checked_champs[d[0]] === true ? "black" : checked_champs[d[0]] === true ? color(i) : "white"}
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
                
                <!-- Select / Deselect All -->
                <g transform = "translate(5, {50 + grouped.size*20} )">
                    <circle
                        r="10" 
                        stroke = "black" 
                        fill={select_deselect_all === true ? "lightblue": "white"}
                        on:click = {uncheck_all}
                    />

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