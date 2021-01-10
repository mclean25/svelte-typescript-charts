<script lang="ts">
  import { onMount } from "svelte";
  import Chart from "chart.js";
  import * as salesData from "../data/timeseries.json";
  import * as goalData from "../data/targetTimeseries.json";

  console.log("sales", salesData);
  console.log("goal", goalData);

  const roundToTwo = (num) => Math.round((num + Number.EPSILON) * 100) / 100;

  function formatTooltip(tooltipItem, data) {
    return formatNumber(tooltipItem.yLabel);
  }

  function formatAxis(label, index, labels) {
    return formatNumber(label);
  }

  function formatNumber(num: number): string {
    return (
      "$" +
      roundToTwo(num / 1000)
        .toString()
        .replace(/\d(?=(\d{3})+\.)/g, "$&,") +
      "k"
    );
  }

  const renderChart = () => {
    const ctx = (<HTMLCanvasElement>(
      document.getElementById("myChart")
    )).getContext("2d");
    window.chart = new Chart(ctx, {
      type: "line",
      data: {
        datasets: [
          {
            label: "Goal",
            data: goalData.goal,
            pointRadius: 0,
            fill: false,
            borderDash: [10, 5],
          },
          {
            label: "My Data",
            data: salesData.sales,
            borderColor: "rgb(43, 51, 96)",
            borderWidth: 2,
            lineTension: 1,
            pointRadius: 0,
            pointHoverRadius: 4,
            pointBorderWidth: 4, // point border width
          },
        ],
      },
      options: {
        hover: {
          intersect: false,
          animationDuration: 0, // setting this back to the default (400) will break it
        },
        tooltips: {
          mode: "x",
          intersect: false,
          callbacks: {
            label: formatTooltip,
          },
        },
        scales: {
          yAxes: [
            {
              ticks: {
                callback: formatAxis,
              },
            },
          ],
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
