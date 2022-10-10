<script>
    export let selected;
    export let index;
    function onChange(element) {
        index = element.target.selectedIndex;
    }
    let options = "ABCDEFG".split("");
    options.push("DP");

    options.map((option,i) => {
        if(selected===option) index=i
    })
</script>

<main>
    <select
        bind:value={selected}
        on:change={onChange}
        class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5"
    >
        {#each options as value}
            <option {value} selected={selected === value}>{value}</option>
        {/each}
    </select>
</main>
