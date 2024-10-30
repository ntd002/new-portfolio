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

<section>
    <h2>My GitHub Stats</h2>
    {#await fetch("https://api.github.com/users/ntd002") }
    <p>Loading...</p>
    {:then response} {#await response.json()}
    <p>Decoding...</p>
    {:then data}
    <dl>
        <div>
            <dt>Followers</dt>
            <dd>{JSON.parse(JSON.stringify(data)).followers}</dd>
        </div>
        <div>
            <dt>Following</dt>
            <dd>{JSON.parse(JSON.stringify(data)).following}</dd>
        </div>
        <div>
            <dt>Public Repos</dt>
            <dd>{JSON.parse(JSON.stringify(data)).public_repos}</dd>
        </div>
        <div>
            <dt>Public Gists</dt>
            <dd>{JSON.parse(JSON.stringify(data)).public_gists}</dd>
        </div>
    </dl>
    {:catch error}
    <p class="error">Something went wrong: {error.message}</p>
    {/await} {:catch error}
    <p class="error">Something went wrong: {error.message}</p>
    {/await}
    
</section>


<h2>Latest Projects</h2>
<div class="projects-highlights">
    {#each projects.slice(0, 3) as p}
    <article>
        <Project data={p} hLevel=3 />
    </article>
    {/each}
</div>

<style>
    .projects-highlights{
        display: grid;
        grid-template-columns: repeat(auto-fill, minmax(15em, 1fr));
        gap: 10px;
        }

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

