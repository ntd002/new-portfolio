<script>
    import projects from '$lib/projects.json';
    import Project from '$lib/Project.svelte';
    import Pie from '$lib/Pie.svelte';
    import * as d3 from 'd3';

    let pieData;
    
    $: {
        // Initialize to an empty object every time this runs
        pieData = {};

        // Calculate rolledData and pieData based on filteredProjects here
        let rolledData = d3.rollups(
        filteredProjects,
        (v) => v.length,
        (d) => d.year,
        );

        pieData = rolledData.map(([year, count]) => {
            return { value: count, label: year }; });
    }
    
    let query = '';

    let filteredProjects;
    $: filteredProjects = projects.filter((project) => {
        let values = Object.values(project).join('\n').toLowerCase();
        
        return values.includes(query.toLowerCase());
    });

    //filtering data when clicking pie
    let selectedYearIndex = -1;
    let selectedYear;
    $: selectedYear =
    selectedYearIndex > -1 ? pieData[selectedYearIndex].label : null;
    
    let filteredByYear;
    $: filteredByYear = projects.filter((project) => {
        if (selectedYear) {
            return project.year === selectedYear;
        }

        return true;
        });

</script>

<svelte:head>
    <title>Projects</title>
</svelte:head>

<h1>{ projects.length } Projects</h1>
<Pie data="{pieData}" bind:selectedIndex="{selectedYearIndex}"/>

<input
  type="search"
  bind:value="{query}"
  aria-label="Search projects"
  placeholder="🔍 Search projects…"
/>

<div class="projects">
    {#each filteredByYear as p}
    <article>
        <!-- <h2>{p.title}</h2>
        <img src={p.image} alt="">
        <p>{p.description}</p> -->

        <Project data={p} />
    </article>
    
    {/each}
</div>

<style>
    input {
        width: 100%;
    }
    
</style>