<script>
    import * as d3 from 'd3';
    import { onMount } from 'svelte';

    let data = [];
    let commits = [];

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

        console.log(commits);
    });
</script>


<svelte:head>
    <title>Meta</title>
</svelte:head>

<h1>Meta</h1>
<p>This page includes stats about the code of this website</p>

<dl class="stats">
    <div>
        <dt>Commits</dt>
        <dd>{commits.length}</dd>
    </div>
    <div>
        <dt>Files</dt>
        <dd>{data.length}</dd>
    </div>
    <div>
        <!-- Correct -->
        <dt>Total <abbr title="Lines of code">LOC</abbr></dt>
        <dd>{data.length}</dd>
    </div>
    <div>
        <dt>Longest Line</dt>
        <dd>{d3.max(data, d => d.depth)}</dd>
    </div>
</dl>

<style>
    dl {
    display: grid;
    grid-template-columns: repeat(4, minmax(0, 1fr));
    }
    dt {
        color: #8E94A3;
    }
    dd {
        font-size: 2.5em;
        text-indent: -1em;
    }
</style>