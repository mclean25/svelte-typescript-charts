<script>
  import { onMount } from "svelte";
  import { Chart } from "chart.js";
  import { goal, actual, actualLastYear } from "./data";

  function buildTooltip(tooltipModel) {
    // Tooltip Element
    var tooltipEl = document.getElementById("chartjs-tooltip-custom");

    // Create element on first render
    if (!tooltipEl) {
      tooltipEl = document.createElement("div");
      tooltipEl.id = "chartjs-tooltip-custom";
      tooltipEl.innerHTML = "<table></table>";
      document.body.appendChild(tooltipEl);
    }

    // Hide if no tooltip
    if (tooltipModel.opacity === 0) {
      tooltipEl.style.opacity = 1;
      return;
    }

    // Set caret Position
    tooltipEl.classList.remove("above", "below", "no-transform");
    if (tooltipModel.yAlign) {
      tooltipEl.classList.add(tooltipModel.yAlign);
    } else {
      tooltipEl.classList.add("no-transform");
    }

    function getBody(bodyItem) {
      return bodyItem.lines;
    }

    // Set Text
    if (tooltipModel.body) {
      console.log("is body");
      var titleLines = tooltipModel.title || [];
      var bodyLines = tooltipModel.body.map(getBody);

      var innerHtml = "<thead>";

      titleLines.forEach(function (title) {
        innerHtml += "<tr><th>" + title + "</th></tr>";
      });
      innerHtml += "</thead><tbody>";

      bodyLines.forEach(function (body, i) {
        var colors = tooltipModel.labelColors[i];
        var style = "background:" + colors.backgroundColor;
        style += "; border-color:" + colors.borderColor;
        style += "; border-width: 2px";
        var span =
          '<span class="chartjs-tooltip-key-custom" style="' +
          style +
          '"></span>';
        innerHtml += "<tr><td>" + span + body + "</td></tr>";
      });
      innerHtml += "</tbody>";

      var tableRoot = tooltipEl.querySelector("table");
      tableRoot.innerHTML = innerHtml;
    }

    // `this` will be the overall tooltip
    var position = this._chart.canvas.getBoundingClientRect();

    // Display, position, and set styles for font
    tooltipEl.style.position = "absolute";
    tooltipEl.style.left =
      position.left + window.pageXOffset + tooltipModel.caretX + "px";
    tooltipEl.style.top =
      position.top + window.pageYOffset + tooltipModel.caretY + "px";
    tooltipEl.style.fontFamily = tooltipModel._bodyFontFamily;
    tooltipEl.style.fontSize = tooltipModel.bodyFontSize + "px";
    tooltipEl.style.fontStyle = tooltipModel._bodyFontStyle;
    tooltipEl.style.padding =
      tooltipModel.yPadding + "px " + tooltipModel.xPadding + "px";
    tooltipEl.style.pointerEvents = "none";
  }

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
    const ctx = document.getElementById("myChart").getContext("2d");
    var chart = new Chart(ctx, {
      type: "line",
      data: {
        datasets: [
          {
            label: " Goal",
            data: goal.goal,
            pointRadius: 0,
            borderColor: "rgb(114, 114, 114)",
            fill: false,
            borderDash: [10, 5],
            borderWidth: 1,
          },
          {
            label: " 2021",
            data: actual.sales,
            fill: false,
            borderColor: "rgb(43, 51, 96)",
            borderWidth: 2,
            pointRadius: 0,
            pointHoverRadius: 4,
            pointBorderWidth: 4,
          },
          {
            label: " 2020",
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
          enabled: false,
          intersect: false,
          custom: buildTooltip,
          position: "nearest",
          // titleSpacing: 5,
          // bodySpacing: 10,
          // xPadding: 10,
          // yPadding: 10,
          // caretPadding: 5,
          // callbacks: {
          //   label: formatLabel,
          //   labelColor: formatLabelColor,
          // },
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

<style>
  canvas {
    -moz-user-select: none;
    -webkit-user-select: none;
    -ms-user-select: none;
  }
  :global(#chartjs-tooltip-custom) {
    opacity: 1;
    position: absolute;
    background: rgba(0, 0, 0, 0.7);
    color: white;
    border-radius: 3px;
    -webkit-transition: all 0.1s ease;
    transition: all 0.1s ease;
    pointer-events: none;
    -webkit-transform: translate(-50%, 0);
    transform: translate(-50%, 0);
  }

  :global(.chartjs-tooltip-key-custom) {
    display: inline-block;
    width: 10px;
    height: 10px;
    margin-right: 10px;
    border-radius: 5px;
  }
</style>

<div class="ChartContainer"><canvas id="myChart" /></div>
