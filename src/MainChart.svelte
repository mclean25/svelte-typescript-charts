<script lang="ts">
  import { onMount } from "svelte";
  import Chart from "chart.js";
  import * as data from "../data/timeseries.json";

  console.log(data);

  const renderChart = () => {
    const ctx = (<HTMLCanvasElement>(
      document.getElementById("myChart")
    )).getContext("2d");
    var myChart = new Chart(ctx, {
      type: "line",
      data: {
        datasets: [
          {
            label: "Demo",
            data: data.default,
          },
        ],
      },
      options: {
        tooltips: {
          mode: "index",
          axis: "x",
          intersect: false,
        },
        scales: {
          xAxes: [
            {
              type: "time",
              distribution: "linear",
              time: {
                unit: "month",
              },
            },
          ],
        },
      },
    });
  };

  onMount(async () => {
    renderChart();
  });
</script>

<div class="ChartContainer"><canvas id="myChart" /></div>
