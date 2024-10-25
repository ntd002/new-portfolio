<script>
    import { page } from '$app/stores';
    let pages = [
    { url: './', title: 'Home' },
    { url: './projects', title: 'Projects' },
    { url: './contact', title: 'Contact' },
    { url: './resume', title: 'Resumes' },
    { url: './https://github.com/ntd002', title: 'Github Profile' },
    ];
</script>
<nav>
    {#each pages as p }
        <a href={p.url}>{p.title}</a>
    {/each}
</nav>


