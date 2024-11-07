<script>
    export let data = {};
    export let hLevel = 2;
</script>

<article>
    <svelte:element this={'h' + hLevel}>{data.title}</svelte:element>   
    <img src={data.image} alt="">
    <div>
        <p>{data.description}</p>
        <p class="year">c. {data.year}</p>
    </div>
</article>

<style>
    .year { 
        font-weight: bold;
        font-variant-numeric: oldstyle-nums;
        color:#666666;
    }

</style>
