<script>
    import * as d3 from 'd3';

    let arcGenerator = d3.arc().innerRadius(0).outerRadius(50);
    let arc = arcGenerator({
    startAngle: 0,
    endAngle: 2 * Math.PI,
    });

    // let data = [1, 2];
    // let total = 0;
    // for (let d of data) {
    // total += d;
    // }

    // let angle = 0;
    // let arcData = [];
    // for (let d of data) {
    // let endAngle = angle + (d / total) * 2 * Math.PI;
    // arcData.push({ startAngle: angle, endAngle });
    // angle = endAngle;
    // }

    // let arcs = arcData.map((d) => arcGenerator(d));

    export let data = [];

    let sliceGenerator = d3.pie().value((d) => d.value);
    let arcData;
    let arcs;

    $:{
        arcData = sliceGenerator(data);
        arcs = arcData.map((d) => arcGenerator(d));
    }

    let colors = d3.scaleOrdinal(d3.schemeTableau10);

    //selecting wedge
    export let selectedIndex = -1;
    function toggleWedge(index, event) {
        if (!event.key || event.key === 'Enter') {
        selectedIndex = selectedIndex === index ? -1 : index;
        }
    }

    let hoveredIndex = -1;
</script>

<div class="container">
    <svg viewBox="-50 -50 100 100">
        {#each arcs as arc, index} 
            <path d={arc} fill={ colors(index)} 
            class:selected={selectedIndex === index} 
            style="opacity: {hoveredIndex === -1 || hoveredIndex === index ? 1 : 0.5};"
            on:mouseenter={() => hoveredIndex = index}
            on:focus={() => hoveredIndex = index}
            on:blur={() => hoveredIndex = -1}
            on:mouseleave={() => hoveredIndex = -1}
            on:click={e => toggleWedge(index, e)}
            on:keyup={e => toggleWedge(index, e)}
            tabIndex="0"
            role="button"
            aria-label
            /> 
        {/each}
    </svg>
    
    <ul class="legend">
        {#each data as d, index}
        <li style="--color: { colors(index) }">
          <span class="swatch" class:selected={selectedIndex === index}></span>
          <!--  -->
          {d.label} <em>({d.value})</em>
        </li>
        {/each}
    </ul>
</div>



<style>
    .container{
        /* display: flex; 
        justify-content: space-between; */
        display: grid; 
        grid-template-columns: repeat(2, auto);
        gap: 20px;
    }

    svg{
    max-width: 20em;
    margin-block: 2em;

    /* Do not clip shapes outside the viewBox */
    overflow: visible;
    }

    ul {
        width: 100%;
        height: 10em;
        background-color:#D9D5C9;
        border-radius: 10px;

        display: grid;
        grid-template-columns: repeat(auto-fill, minmax(8em, 1fr));
        overflow: hidden;
    }
    li {
        display: flex;
        align-items: center;
        gap: 2px;

        padding-left: 10px;  /* Add space for custom marker */
        position: relative;
    }

    li::before {
        content: '';            /* Custom bullet */
        position: absolute;
        left: 0;                /* Place the bullet at the beginning of the list item */
        top: 50%;               /* Vertically center the bullet */
        transform: translateY(-50%);
        width: 8px;             /* Bullet size */
        height: 8px;            /* Bullet size */
        background-color: var(--color); /* Bullet color */
        border-radius: 50%;     /* Make the bullet circular */
    }

    /* svg:has(path:hover) {
        path:not(:hover) {
            opacity: 50%;
        }
    } */

    /* works but any area of svg highlighted */
    /* svg:hover{
        path:not(:hover) {
            opacity: 50%;
        }
    } */

    /* works but stays grayed out without overing */
    /* svg{
        path:not(:hover, :focus-visible) {
            opacity: 50%;
        }
    } */

    /* Lab */
    /* svg:has(path:hover,  path:focus-visible) {
        path:not(:hover, :focus-visible) {
            opacity: 50%;
        }
    } */
     /* ChatGPT */
    /* svg:has(path:hover, path:focus-visible) {
    path:not(:hover):not(:focus-visible) {
        opacity: 0.5;
    }
    } */






    path {
        transition: 300ms;
        cursor: pointer;
        outline: none;
    }

    /* path:not(:hover) {
        opacity: 50%;
    } */
    
    .selected {
    --color: oklch(60% 45% 0) !important;

    &:is(path) {
        fill:#18864B;
        }
    }

</style>