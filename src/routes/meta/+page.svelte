<script>
    import * as d3 from 'd3';
    import { onMount } from 'svelte';
    import { computePosition, autoPlacement, offset } from '@floating-ui/dom';
    import Pie from '$lib/Pie.svelte';

    let data = [];
    let commits = [];
    let longLine = 0;
    $: longLine = d3.max(data, d => d.length)
    let numFiles = 0;
    $: numFiles = d3.group(data, d => d.file).size;

    onMount(async () => {
        data = await d3.csv('loc.csv', (row) => ({
        ...row,
        line: Number(row.line), // or just +row.line
        depth: Number(row.depth),
        length: Number(row.length),
        date: new Date(row.date + 'T00:00' + row.timezone),
        datetime: new Date(row.datetime),
        }));

        commits = d3
        .groups(data, (d) => d.commit)
        .map(([commit, lines]) => {
            let first = lines[0];
            let { author, date, time, timezone, datetime } = first;
            let ret = {
            id: commit,
            url: 'https://github.com/vis-society/lab-7/commit/' + commit,
            author,
            date,
            time,
            timezone,
            datetime,
            hourFrac: datetime.getHours() + datetime.getMinutes() / 60,
            totalLines: lines.length,
            };

            // Like ret.lines = lines
            // but non-enumerable so it doesn’t show up in JSON.stringify
            Object.defineProperty(ret, 'lines', {
            value: lines,
            configurable: true,
            writable: true,
            enumerable: false,
            });

            return ret;
        });

        

        commits = d3.sort(commits, (d) => -d.totalLines);
    });

    //scatterplot
    let width = 1000,
    height = 600;
    let margin = { top: 10, right: 10, bottom: 30, left: 20 };
    let usableArea = {
    top: margin.top,
    right: width - margin.right,
    bottom: height - margin.bottom,
    left: margin.left,
    };
    usableArea.width = usableArea.right - usableArea.left;
    usableArea.height = usableArea.bottom - usableArea.top;
    
    let xAxis, yAxis;
    $: xScale = d3  
        .scaleTime()
        .domain(d3.extent(data, (d) => d.date))
        .nice()
        .range([usableArea.left, usableArea.right]);
    $: yScale = d3  
        .scaleLinear()
        .domain([0, 24])
        .nice()
        .range([usableArea.bottom, usableArea.top]);
    $: {
        d3.select(xAxis).call(d3.axisBottom(xScale));
        d3.select(yAxis)
        .call(d3
            .axisLeft(yScale)
            .tickFormat((d) => String(d % 24)
                .padStart(2, '0') + ':00'),);
    }

    let yAxisGridlines;
    $: {
        d3.select(yAxisGridlines)
            .call(d3
                .axisLeft(yScale)
                .tickFormat('')
                .tickSize(-usableArea.width),
            );
    }

    //tooltip
    let hoveredIndex = -1;
    $: hoveredCommit = commits[hoveredIndex] ?? hoveredCommit ?? {};
    function convertTime(time24) {
        if (typeof time24 === "undefined") {
            // Run your code here
            return ""
        }
        // Split the time string into hours and minutes
        const [hours, minutes] = time24.split(":");

        // Convert hours to number
        let hour = parseInt(hours);

        // Determine AM/PM
        const ampm = hour >= 12 ? "PM" : "AM";

        // Adjust hour for 12-hour format
        hour = hour % 12 || 12;

        // Format the time string
        const time12 = `${hour}:${minutes} ${ampm} ${"PST"}`;

        return time12;
    }
    let commitTooltip;
    let tooltipPosition = { x: 0, y: 0 };
    async function dotInteraction(index, evt) {

        let hoveredDot = evt.target;

        if (evt.type === 'mouseenter' || evt.type === 'focus') {
            
            tooltipPosition = await computePosition(hoveredDot, commitTooltip, {
            strategy: 'fixed', // because we use position: fixed
            middleware: [
                offset(5), // spacing from tooltip to dot
                autoPlacement(), // see https://floating-ui.com/docs/autoplacement
            ],
            });

            hoveredIndex = index;
        } else if (evt.type === 'mouseleave' || evt.type === 'blur') {
            hoveredIndex = -1;
        }
    }

    //dot size
    $: rScale = d3  
        .scaleSqrt()
        .domain(d3.extent(data, (d) => d.length))
        .range([2,30]);
    
    //brush
    let svg;
    let brushSelection;
    let brushedID;
    $: {
        d3.select(svg).call(d3.brush().on('start brush end', brushed));
        d3.select(svg).selectAll('.dots, .overlay ~ *').raise();
    }
    function brushed(evt) {
        brushSelection = evt.selection;
    }
    function isCommitSelected(commit) {
        if (!brushSelection) {
            return false;
        }
        let min = { x: brushSelection[0][0], y: brushSelection[0][1] };
        let max = { x: brushSelection[1][0], y: brushSelection[1][1] };
        let x = xScale(commit.date);
        let y = yScale(commit.hourFrac);

        return x >= min.x && x <= max.x && y >= min.y && y <= max.y;
    }
    $: selectedCommits = brushSelection ? commits.filter(isCommitSelected) : [];
    $: hasSelection = brushSelection && selectedCommits.length > 0;

    $: {
        brushedID = selectedCommits.map(item => item.id);
        
        d3.selectAll("circle")
        .each(function() {
            const circleId = this.id; // Get the ID of the current circle
            const isSelected = brushedID.includes(circleId); // Check if ID is in the selected array

            if (isSelected) {
                d3.select(this).attr("fill", "tomato");  // Add the 'selected' class
            } else {"#F16456"
                d3.select(this).attr("fill", "steelblue"); // Remove the 'selected' class
            }
        });
    }

    //language breakdown
    let selectedLines;
    let languageBreakdown;
    let pieData;
    $: selectedLines = (hasSelection ? selectedCommits : commits).flatMap(
        (d) => d.lines,
        );
    $: languageBreakdown = d3.rollups(
        selectedLines,
        (v) => v.length,
        (d) => d.type,
        );
    $: pieData = Array.from(languageBreakdown).map(([language, lines]) => ({label: language, value: lines+" lines"}));
</script>


<svelte:head>
    <title>Meta</title>
</svelte:head>

<h1>Meta</h1>
<p>This page includes stats about the code of this website</p>

<dl class="stats">
    <div class="stats">
        <dt>Commits</dt>
        <dd>{commits.length}</dd>
    </div>
    <div class="stats">
        <dt>Files</dt>
        <dd>{numFiles}</dd>
    </div>
    <div class="stats">
        <!-- Correct -->
        <dt>Total <abbr title="Lines of code">LOC</abbr></dt>
        <dd>{data.length}</dd>
    </div>
    <div class="stats">
        <dt>Longest Line</dt>
        <dd>{longLine}</dd>
    </div>
</dl>

<h2>Commits by time of day</h2>
<svg bind:this={svg} viewBox="0 0 {width} {height}">
    <g
    class="gridlines"
    transform="translate({usableArea.left}, 0)"
    bind:this={yAxisGridlines}
    />
    <g transform="translate(0, {usableArea.bottom})" bind:this={xAxis} />
    <g transform="translate({usableArea.left}, 0)" bind:this={yAxis} />
    <g class="dots">
        {#each commits as commit, index }
        <circle
          id="{commit.id}"
          cx="{xScale(commit.datetime)}"
          cy="{yScale(commit.hourFrac)}"
          r="{rScale(commit.totalLines)}"
          fill="steelblue"
          on:mouseenter={evt => dotInteraction(index, evt)} 
          on:mouseleave={evt => dotInteraction(index, evt)}
          tabindex="0"
          aria-describedby="commit-tooltip"
          role="tooltip"
          aria-haspopup="true"
          on:focus={evt => dotInteraction(index, evt)} 
          on:blur={evt => dotInteraction(index, evt)} 
          />
        {/each}
      </g>
</svg>
<p>{hasSelection ? selectedCommits.length : "No"} commits selected</p>
<Pie data={pieData} />
<dl class="language">
    {#each languageBreakdown as [language, lines] }
        <div class="language">
            <dt>{language}</dt>
            <dd>{lines} lines ({(lines*100 / selectedLines.length).toFixed(2)}%)</dd>
        </div>
    {/each}
</dl>



<dl id="commit-tooltip" 
class="info tooltip" 
hidden={hoveredIndex === -1} 
style="top: {tooltipPosition.y}px; left: {tooltipPosition.x}px"
bind:this="{commitTooltip}"
>
    <dt class="tooltip-title">Commit</dt>
    <dd class="tooltip-item">
      <a href="{ hoveredCommit.url }" target="_blank">{ hoveredCommit.id }</a>
    </dd>

    <dt class="tooltip-title">Author</dt>
    <dd class="tooltip-item">
        {hoveredCommit.author}
    </dd>
  
    <dt class="tooltip-title">Date</dt>
    <dd class="tooltip-item">
        { hoveredCommit.datetime?.toLocaleString("en", {dateStyle: "full"}) }
    </dd>

    <dt class="tooltip-title">Time</dt>
    <dd class="tooltip-item">
        { convertTime(hoveredCommit.time)}
    </dd>

    <dt class="tooltip-title">Lines</dt>
    <dd class="tooltip-item">
        {hoveredCommit.totalLines}
    </dd>
  
</dl>

<style>
    dl.stats {
    display: grid;
    grid-template-columns: repeat(4, minmax(0, 1fr));
    }
    div.stats > dt {
        color: #8E94A3;
    }
    div.stats > dd {
        font-size: 2.5em;
        text-indent: -1em;
    }
    svg {
        overflow: visible;
    }
    .gridlines {
        stroke-opacity: 0.2;
    }
    dl.info {
        display: grid;
        transition-duration: 500ms;
        transition-property: opacity, visibility;

        &[hidden]:not(:hover, :focus-within) {
            opacity: 0;
            visibility: hidden;
        }
    }

    .tooltip {
        grid-template-columns: auto auto;
        position: fixed;
        top: 1em;
        left: 1em;
        display: inline-grid;
        background-color: oklch(from #8E94A3 l c h / 0.1);
        border-radius: 5px;
        padding: 2.5px 5px;
    }
    .tooltip-title {
        font-size: 10px;
        color: #8E94A3;
        justify-self: end;
    }
    .tooltip-item {
        font-size: 10px;
        justify-self: start;
        margin-left: 5px;
    }
    a {
        padding-left: 0px;
        margin-left: 0px;
    }
    circle {
        transition: 200ms;
        transform-origin: center;
        transform-box: fill-box;
        fill-opacity: 70%;
        &:hover {
            transform: scale(1.5);
            fill-opacity: 100%;
        }
    }

    @keyframes marching-ants {
        to {
            stroke-dashoffset: -8; /* 5 + 3 */
        }
    }

    svg :global(.selection) {
    fill-opacity: 10%;
    stroke: black;
    stroke-opacity: 70%;
    stroke-dasharray: 5 3;
    animation: marching-ants 2s linear infinite;
    }

    dl.language {
    display: grid;
    grid-template-columns: repeat(4, minmax(0, 1fr));
    }
    div.language > dt {
        font-size: 1em;
        color: #8E94A3;
    }
    div.language > dd {
        font-size: 1em;
        font-weight: 500;
    }
    
</style>