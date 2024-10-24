<script>
    import projects from '$lib/projects.json';
    import Project from '$lib/Project.svelte';
</script>

<svelte:head>
    <title>Ntd002: Personal site and portfolio</title>
</svelte:head>

<h1>Nathaniel Do</h1>
<p>I bake, code, and am painting my house.</p>
<img class="big_image" src="images/chicken parm.jpg" alt="Chicken Parmesan that looks better than one you've had before">

<h3>Latest Projects</h3>
<div class="projects-highlights">
    {#each projects.slice(0, 3) as p}
    <article>
        <Project data={p} />
    </article>
    {/each}
</div>

<style>
    .projects-highlights{
        display: grid;
        grid-template-columns: repeat(auto-fill, minmax(15em, 1fr));
        gap: 10px;
        }
</style>

