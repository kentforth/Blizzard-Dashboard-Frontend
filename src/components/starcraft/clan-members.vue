<template>
  <apexchart
    type="bar"
    height="380"
    :options="chartOptions"
    :series="series"
  ></apexchart>
</template>

<script>
export default {
  name: "clan-members",
  data: () => ({
    series: [
      {
        data: [400, 430, 448, 470, 540, 580, 690, 1100, 1200, 1380],
      },
    ],
    chartOptions: {
      chart: {
        type: "bar",
        height: 380,
        foreColor: "#f4e2e2",
      },
      plotOptions: {
        bar: {
          barHeight: "100%",
          distributed: true,
          horizontal: true,
          dataLabels: {
            position: "bottom",
          },
        },
      },
      colors: [
        "#33b2df",
        "#546E7A",
        "#d4526e",
        "#13d8aa",
        "#A5978B",
        "#2b908f",
        "#f9a3a4",
        "#90ee7e",
        "#f48024",
        "#69d2e7",
      ],
      dataLabels: {
        enabled: true,
        textAnchor: "start",
        style: {
          colors: ["#fff"],
        },
        formatter: function (val, opt) {
          return opt.w.globals.labels[opt.dataPointIndex] + ":  " + val;
        },
        offsetX: 0,
        dropShadow: {
          enabled: true,
        },
      },
      stroke: {
        width: 1,
        colors: ["#fff"],
      },
      xaxis: {
        categories: [
          "ValidG",
          "Alpha X",
          "TiminG CC",
          "Star Killer",
          "Ice Hus",
          "Eivici Corp",
          "ZTemp",
          "Starlight Twinkie",
          "Pariahs",
          "Cosmic Cloud",
        ],
      },
      yaxis: {
        labels: {
          show: false,
        },
      },
      title: {
        text: "Clan Members",
        align: "center",
        floating: true,
      },
      tooltip: {
        theme: "dark",
        x: {
          show: false,
        },
        y: {
          title: {
            formatter: function () {
              return "";
            },
          },
        },
      },
    },
  }),
  created() {
    this.$socket.client.on("clanMembers", (data) => {
      this.series[0].data = data;
      this.series = [
        {
          data: this.series[0].data,
        },
      ];
    });
  },
  beforeDestroy() {
    this.$socket.client.off("clanMembers");
  },
};
</script>

<style scoped></style>
