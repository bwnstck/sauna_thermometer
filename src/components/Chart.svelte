<script>
  import { Chart } from "chart.js/auto";
  import addMinutes from "date-fns/addMinutes";
  import format from "date-fns/format";
  import { onMount } from "svelte";

  export let chartValues;
  export let startTime;
  let chartLabels = chartValues?.map((_item, i) =>
    format(addMinutes(startTime, i * 10), "HH:mm")
  );
  let ctx;
  let chartCanvas;

  onMount(async () => {
    ctx = chartCanvas.getContext("2d");

    new Chart(ctx, {
      type: "line",
      //   options: {
      //     scales: {
      //       scrollY: {
      //         beginAtZero: true,
      //       },
      //     },
      //   },
      data: {
        labels: chartLabels,
        datasets: [
          {
            label: "Temperatur",
            backgroundColor: "rgb(255, 99, 132)",
            borderColor: "rgb(255, 99, 132)",
            data: chartValues,
          },
        ],
      },
    });
  });
</script>

<canvas bind:this={chartCanvas} id="myChart" />
