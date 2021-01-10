<script lang="ts">
  import { onMount } from "svelte";
  import { Chart } from "chart.js";
  import { goal, actual, actualLastYear } from "./data";

  const roundToTwo = (num) => Math.round((num + Number.EPSILON) * 100) / 100;

  function formatLabel(tooltipItem, chart) {
    const label = chart.datasets[tooltipItem.datasetIndex].label;
    return label + ":  " + formatNumber(tooltipItem.value);
  }

  function formatLabelColor(tooltipItem, chart) {
    var color =
      chart.config.data.datasets[tooltipItem.datasetIndex].borderColor;
    return {
      borderColor: color,
      backgroundColor: color,
    };
  }

  function formatAxis(label, index, labels) {
    return formatNumber(label);
  }

  function formatNumber(num) {
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
    var chart = new Chart(ctx, {
      type: "line",
      data: {
        datasets: [
          {
            label: "Goal",
            data: goal.goal,
            pointRadius: 0,
            borderColor: "rgb(114, 114, 114)",
            fill: false,
            borderDash: [10, 5],
            borderWidth: 1,
          },
          {
            label: "2021",
            data: actual.sales,
            fill: false,
            borderColor: "rgb(43, 51, 96)",
            borderWidth: 2,
            pointRadius: 0,
            pointHoverRadius: 4,
            pointBorderWidth: 4,
          },
          {
            label: "2020",
            fill: false,
            data: actualLastYear.sales,
            borderColor: "rgb(189, 198, 228)",
            borderWidth: 2,
            pointRadius: 0,
            pointHoverRadius: 4,
            pointBorderWidth: 4,
          },
        ],
      },
      options: {
        hover: {
          intersect: false,
          animationDuration: 0, // setting this back to the default (400) will break it
        },
        tooltips: {
          mode: "index",
          intersect: false,
          position: "nearest",
          titleSpacing: 5,
          bodySpacing: 10,
          xPadding: 10,
          yPadding: 10,
          caretPadding: 5,
          callbacks: {
            label: formatLabel,
            labelColor: formatLabelColor,
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
