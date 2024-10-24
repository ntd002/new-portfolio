<script>
    import projects from '$lib/projects.json';
    import Project from '$lib/Project.svelte';
</script>

<svelte:head>
    <title>Projects</title>
</svelte:head>

<h1>{ projects.length } Projects</h1>
<div class="projects">
    {#each projects as p}
    <article>
        <!-- <h2>{p.title}</h2>
        <img src={p.image} alt="">
        <p>{p.description}</p> -->

        <Project data={p} />
    </article>
    
    {/each}
    <!-- <article>
        <h2>Velit aliquid veniam nemo.</h2>
        <img src="https://vis-society.github.io/labs/2/images/empty.svg" alt="">
        <p>Molestias accusantium optio veniam mollitia, omnis quis sequi voluptatem cumque voluptatum perferendis, ipsam saepe. Totam ipsum repellendus eius enim eligendi id neque dolores harum corrupti, animi voluptatum modi quibusdam minima?</p>
    </article>
    <article>
        <h2>Ullam, non. Alias, quidem.</h2>
        <img src="https://vis-society.github.io/labs/2/images/empty.svg" alt="">
        <p>Consequatur sunt neque illum, magnam molestias doloribus totam cumque officiis temporibus quaerat? Pariatur perferendis, eveniet harum rem voluptate voluptatum sint numquam, et voluptates quia asperiores veniam aliquid impedit nemo labore.</p>
    </article>
    <article>
        <h2>Enim necessitatibus ut voluptas?</h2>
        <img src="https://vis-society.github.io/labs/2/images/empty.svg" alt="">
        <p>Reprehenderit ad necessitatibus sit, adipisci est ipsum dolor reiciendis fugit? Ratione impedit cum, at, deserunt corrupti id beatae quo aliquam distinctio nobis tempore quis dicta vero laborum! Corrupti, blanditiis fugit!</p>
    </article>
    <article>
        <h2>Debitis nobis assumenda alias.</h2>
        <img src="https://vis-society.github.io/labs/2/images/empty.svg" alt="">
        <p>Eligendi sequi dignissimos magni distinctio sapiente numquam quibusdam error eveniet quidem optio laboriosam cupiditate beatae fugit molestias itaque, facere animi ut ducimus amet similique obcaecati fugiat illum rerum mollitia! Beatae.</p>
    </article>
    <article>
        <h2>Delectus ducimus sequi consequatur!</h2>
        <img src="https://vis-society.github.io/labs/2/images/empty.svg" alt="">
        <p>Neque, eaque illo? Reprehenderit dolore iusto consectetur, illum, magni ipsa laborum consequuntur dicta praesentium provident ipsum non ex voluptatum laudantium veritatis! Ipsam consequatur placeat explicabo perspiciatis, commodi aperiam cumque eligendi!</p>
    </article>
    <article>
        <h2>Omnis non fugiat nam?</h2>
        <img src="https://vis-society.github.io/labs/2/images/empty.svg" alt="">
        <p>Aspernatur laboriosam cumque eos fuga debitis id ullam, perspiciatis accusamus quas cupiditate inventore cum. Labore voluptas quisquam possimus sapiente illo deserunt facilis dicta nesciunt? Fugit cumque nostrum facilis. Dolorum, placeat!</p>
    </article>
    <article>
        <h2>Facilis facere voluptatum quidem?</h2>
        <img src="https://vis-society.github.io/labs/2/images/empty.svg" alt="">
        <p>At veritatis tenetur libero, in nostrum optio consequatur autem earum praesentium eaque inventore quisquam distinctio odio accusamus voluptatem? Assumenda vitae ducimus odio inventore sapiente, nostrum molestias quod esse quam consectetur.</p>
    </article>
    <article>
        <h2>Suscipit aspernatur voluptatibus labore.</h2>
        <img src="https://vis-society.github.io/labs/2/images/empty.svg" alt="">
        <p>Voluptatem dolor totam, dolore assumenda nulla ea doloribus officia! Nesciunt et necessitatibus autem eos ipsa fugit ipsum ullam illo quisquam quasi animi saepe suscipit ab, aliquid impedit cumque eum magni!</p>
    </article>
    <article>
        <h2>Ipsum architecto ab odio!</h2>
        <img src="https://vis-society.github.io/labs/2/images/empty.svg" alt="">
        <p>Quaerat repellendus quis, sequi libero dicta tempore quos culpa. Modi culpa quasi dolore nobis quas. Hic fuga voluptas cumque praesentium delectus atque maiores error, voluptatem possimus ullam minima modi fugit!</p>
    </article>
    <article>
        <h2>Odit doloribus ratione eum.</h2>
        <img src="https://vis-society.github.io/labs/2/images/empty.svg" alt="">
        <p>Accusantium, omnis numquam mollitia cum consectetur molestiae! Eum quam ratione porro ducimus consequatur, fugit pariatur. Magnam voluptas assumenda soluta, quod suscipit aperiam est velit, minima odio dolor ut provident atque!</p>
    </article>
    <article>
        <h2>Doloremque iusto ab repellendus!</h2>
        <img src="https://vis-society.github.io/labs/2/images/empty.svg" alt="">
        <p>Sit mollitia, repudiandae fuga a facere sequi adipisci magni quasi molestiae quae accusantium odio, deleniti ipsam officiis aperiam excepturi quas maiores? Doloribus quos sint asperiores hic expedita modi, perspiciatis fugit?</p>
    </article> -->
</div>