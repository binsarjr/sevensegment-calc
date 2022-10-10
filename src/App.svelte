<script>
  import { onMount } from "svelte";
  import Header from "./lib/components/Header.svelte";
  import Order from "./lib/components/Order.svelte";
  let type = "common_anoda";
  let output = "";
  const default_segment_orders = ["DP", "G", "F", "E", "D", "C", "B", "A"];
  let segment_orders = default_segment_orders.slice();
  let segment_indexs = [];

  function autoresize() {
    const textarea = document.getElementById("output");

    if (textarea) {
      textarea.style.height = "5px";
      textarea.style.height = textarea.scrollHeight + "px";
    }
  }
  onMount(() => {
    autoresize();
  });

  $: autoresize();

  $: {
    output = "";

    let seg_matrix = [
      //a  b  c  d  e  f  g  dp
      [1, 1, 1, 1, 1, 1, 0, 0], // 0
      [0, 1, 1, 0, 0, 0, 0, 0], // 1
      [1, 1, 0, 1, 1, 0, 1, 0], // 2
      [1, 1, 1, 1, 0, 0, 1, 0], // 3
      [0, 1, 1, 0, 0, 1, 1, 0], // 4
      [1, 0, 1, 1, 0, 1, 1, 0], // 5
      [1, 0, 1, 1, 1, 1, 1, 0], // 6
      [1, 1, 1, 0, 0, 0, 0, 0], // 7
      [1, 1, 1, 1, 1, 1, 1, 0], // 8
      [1, 1, 1, 1, 0, 1, 1, 0], // 9
    ];
    output = "uint8_t seg_table[] = {\n";
    output += "\t//" + segment_orders.join(",") + "\n";
    for (let i = 0; i < 10; i++) {
      output += "\t0b";
      if (type === "common_anoda") {
        // common anode
        for (let j = 0; j < 8; j++)
          output += seg_matrix[i][segment_indexs[j]] * -1 + 1;
      } else if (type === "common_cathoda") {
        // common cathodes
        for (let j = 0; j < 8; j++) output += seg_matrix[i][segment_indexs[j]];
      }

      if (i < 9) output += ",";
      output += "\t // " + i + "\n";
    }
    output += "}";
  }
</script>

<main class="md:mobile-view flex flex-col gap-1">
  <Header />
  <div class="bg-purple-200 p-2">
    <ul class="list-decimal list-inside">
      <li>select indicator type (common anode or common cathode);</li>
      <li>select segment order;</li>
    </ul>
  </div>
  <div class="grid grid-cols-[0.25fr,1fr] gap-1 text-sm" id="layout">
    <div>Type</div>
    <div>
      <div>
        <input
          type="radio"
          bind:group={type}
          name="type"
          value="common_anoda"
          id="common_anoda"
        /> <label for="common_anoda">Common Anoda</label>
      </div>
      <div>
        <input
          type="radio"
          bind:group={type}
          name="type"
          value="common_cathoda"
          id="common_cathoda"
        /> <label for="common_cathoda">Common Cathoda</label>
      </div>
    </div>
    <div>Segment Order</div>
    <div class="grid grid-cols-8 text-center">
      {#each segment_orders as _, i}
        <div>{segment_orders.length - 1 - i}</div>
      {/each}
    </div>
    <div />
    <div class="grid grid-cols-8 text-center">
      {#each segment_orders as segment_order, i}
        <Order bind:selected={segment_order} bind:index={segment_indexs[i]} />
      {/each}
    </div>
    <div />
    <div>
      <button
        class="px-4 py-2 bg-gray-50 rounded hover:bg-gray-200"
        on:click={() => {
          segment_orders = default_segment_orders.slice();
        }}>Reset</button
      >
      <button
        class="px-4 py-2 bg-gray-50 rounded hover:bg-gray-200"
        on:click={() => {
          segment_orders = segment_orders.reverse();
        }}>Reverse</button
      >
    </div>
  </div>
  <div class="bg-purple-200 text-center p-2">Output</div>
  <textarea id="output" class="w-full p-2 rounded bg-purple-200"
    >{output}</textarea
  >
</main>

<style>
  #layout > div {
    @apply bg-purple-200 p-2;
  }
</style>
