<template>
  <div class="users-area">
    <apexchart
      type="area"
      height="200"
      :options="usersChartOptions"
      :series="usersSeries"
      ref="usersChart"
      class="all-users-chart"
    ></apexchart>
    <h3>Change users number</h3>
    <span>Max: {{ slider }}</span>
    <vue-slider
      class="my-slider"
      v-model="slider"
      :min="5000"
      :max="50000"
      :interval="1000"
      @drag-end="setValue"
    />
    <!--SPINNER-->
    <spinner class="spinner" :color="spinnerColor" v-if="hasSpinner" />
  </div>
</template>

<script>
import Spinner from "../../components/spinner";

import VueSlider from "vue-slider-component";
import "vue-slider-component/theme/antd.css";

export default {
  name: "users-area",
  components: {
    Spinner,
    VueSlider,
  },
  data: () => ({
    slider: "5000",
    spinnerColor: "#DAA011",
    hasSpinner: true,
    spinnerInterval: null,
    socket: null,
    connection: null,
    usersSeries: [
      {
        name: "Users",
        data: [],
      },
    ],
    usersChartOptions: {
      chart: {
        type: "area",
        foreColor: "#FFEDFF",
        animations: {
          enabled: true,
          easing: "easeinout",
          speed: 800,
          animateGradually: {
            enabled: true,
            delay: 150,
          },
          dynamicAnimation: {
            enabled: true,
            speed: 350,
          },
        },
      },
      colors: ["#DAA011"],
      dataLabels: {
        enabled: false,
      },
      stroke: {
        curve: "smooth",
      },
      grid: {
        show: false,
      },
      yaxis: {
        show: false,
      },
      xaxis: {
        type: "datetime",
        tickAmount: 9,
        labels: {
          format: "HH:mm:ss",
          datetimeUTC: false,
        },
      },
      tooltip: {
        theme: "dark",
        x: {
          format: "HH:mm:ss",
        },
      },
    },
  }),
  created() {
    this.$socket.client.on("newNumber", (user) => {
      this.addUser(user);
    });

    this.spinnerInterval = setInterval(() => {
      this.hasSpinner = false;
    }, 4500);
  },
  beforeDestroy() {
    clearInterval(this.spinnerInterval);
    this.$socket.client.off("newNumber");
  },
  methods: {
    addUser(user) {
      if (this.usersSeries[0].data.length >= 20) {
        this.usersSeries[0].data.shift();
      } else {
        this.usersSeries[0].data.push(user);
      }

      this.usersSeries = [
        {
          data: this.usersSeries[0].data,
        },
      ];
    },
    setValue() {
      this.hasSpinner = true;
      this.spinnerInterval = setInterval(() => {
        this.hasSpinner = false;
      }, 5000);
      this.usersSeries[0].data = [];
      this.$socket.client.emit("changeUsersNumber", this.slider);
    },
  },
};
</script>

<style lang="scss">
.spinner {
  position: absolute;
  top: 10%;
  left: 45%;
}

h3 {
  margin: 0 0 10px 0;
}

.vue-slider-process,
.vue-slider-dot-handle {
  background-color: $overwatch-accent !important;
}

.vue-slider-dot-handle {
  border: 2px solid $overwatch-accent !important;
}
</style>
