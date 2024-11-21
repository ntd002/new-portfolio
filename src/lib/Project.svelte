<script>
    export let data = {};
    export let hLevel = 2;
</script>

<article>
    <svelte:element this={'h' + hLevel}><a href={data.url} target=_blank>{data.title}</a></svelte:element>   
    <img src={data.image} alt="">
    <div>
        <p>{data.description}</p>
        <p class="year">c. {data.year}</p>
    </div>
</article>

<style>
    a {
        padding: 0px;
    }
    .year { 
        font-weight: bold;
        font-variant-numeric: oldstyle-nums;
        color:#666666;
    }

</style>
