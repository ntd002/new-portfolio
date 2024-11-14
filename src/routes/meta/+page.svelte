<script>
    import * as d3 from 'd3';
    import { onMount } from 'svelte';
    import { computePosition, autoPlacement, offset } from '@floating-ui/dom';

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

        

        // console.log(data);
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
<svg viewBox="0 0 {width} {height}">
    <g
    class="gridlines"
    transform="translate({usableArea.left}, 0)"
    bind:this={yAxisGridlines}
    />
    <g transform="translate(0, {usableArea.bottom})" bind:this={xAxis} />
    <g transform="translate({usableArea.left}, 0)" bind:this={yAxis} />
    <g class="dots">
        {console.log(commits.length)}
        {#each commits as commit, index }
        <circle
          cx="{
          xScale(commit.datetime)
          }"
          cy="{
          yScale(commit.hourFrac)
          }"
          r="5"
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
        &:hover {
            transform: scale(1.5);
        }
    }
</style>