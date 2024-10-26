<script>
    import { page } from '$app/stores';
    import '../style.css';
    let pages = [
    { url: './', title: 'Home' },
    { url: './projects', title: 'Projects' },
    { url: './contact', title: 'Contact' },
    { url: './resume', title: 'Resume' },
    { url: 'https://github.com/ntd002', title: 'Github Profile', },
    ];
</script>

<nav>
    {#each pages as p }
        <a href={p.url} class:current={'.' + $page.route.id === p.url} target={ p.url.startsWith("http") ? "_blank" : null }>
            {p.title}
        </a>
    {/each}
</nav>
<slot />

<style>
    nav {
    display: flex;
    margin-bottom: 5em;
    border-bottom-width: 1px;
    border-bottom-style: solid;
    --border-color: oklch(50% 10% 200 / 40%);
    border-bottom-color: var(--border-color);
    }
    a {
    flex: 1;
    text-decoration: none;
    color: inherit;
    text-align: center;
    padding: 0.5em;
    }

    a:hover {
    border-bottom-width: 0.4em;
    border-bottom-style: solid;
    border-bottom-color: var(--color-accent);
    background-color: color-mix(in oklch, var(--color-accent), canvas 85%);
    }

    a.current {
    border-bottom-width: 0.4em;
    border-bottom-style: solid;
    border-bottom-color: oklch(90% 3% 200);
    }
</style>