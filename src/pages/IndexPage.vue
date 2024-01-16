<template>
  <q-page class="flex flex-center">
    <div  class="q-px-md full-width">
      <div class="text-h6 text-weight-bold text-center full-width q-mb-md" >
        Bodyweight Tracker
      </div>
      <div style="max-width: 400px" class="q-mx-auto">
        <q-input
          class="q-mb-md"
          outlined
          v-model="weightInput"
          color="info"
          label-color="info"
          label="Enter Weights (kg)"
          hint="Enter weights separated by spaces (e.g., 70 70.2 70.1 70.4)"
        />

        <q-input
          filled
          readonly
          v-model="startDate"
          mask="date"
          label="Start Date"
          :rules="[validateDate]"
        >
          <template v-slot:append>
            <q-icon
              name="event"
              class="cursor-pointer"
              hint="Press calendar symbol to choose a start date"
            >
              <q-popup-proxy
                cover
                transition-show="scale"
                transition-hide="scale"
              >
                <q-date v-model="startDate">
                  <div class="row items-center justify-end">
                    <q-btn v-close-popup label="Close" color="primary" flat />
                  </div>
                </q-date>
              </q-popup-proxy>
            </q-icon>
          </template>
        </q-input>
        <q-btn
          label="Generate Graph"
          type="primary"
          class="shadow-1 q-mt-sm q-mb-lg full-width"
          no-caps
          padding="md lg"
          color="accent"
          @click="generateGraph"
          unelevated
        />
      </div>
      <div v-if="graphVisible" style="max-width: 800px" class="q-mx-auto q-mt-xl"> 
        <ApexCharts
          style="max-width: 100%"
          :options="options"
          :series="options.series"
        ></ApexCharts>
      </div>
    </div>
  </q-page>
</template>

<script>
import { defineComponent, ref } from "vue";
import ApexCharts from "vue3-apexcharts";

export default defineComponent({
  name: "BodyweightTracker",
  components: { ApexCharts },
  setup() {
    var weightInput = ref("");
    var startDate = ref("");
    const graphVisible = ref(false);

    var options = {
      chart: {
        type: "line",
      },
      series: [
        {
          name: "Series 1",
          data: [],
        },
      ],
      xaxis: {
        title: { text: "Date" },
        type: "datetime",
      },
      yaxis: {
        title: { text: "Weight (kg)" },
      },
      title: {
        text: "Bodyweight trend since",
      },
      stroke: {
        curve: "smooth",
      },
    };

    const validateDate = (val) => {
      return val && !isNaN(Date.parse(val)) ? true : "Invalid date";
    };

    const generateGraph = () => {
      if (!validateDate(startDate.value)) {
        alert("Please enter a valid start date.");
        return;
      }

      const weights = weightInput.value.trim().split(" ").map(Number);
      if (weights.some(isNaN)) {
        alert("Please enter valid weights.");
        return;
      }

      const dateLabels = generateDateLabels(
        weights.length,
        new Date(startDate.value)
      );

      if (dateLabels.length !== weights.length) {
        alert("Date labels and weights count mismatch.");
        return;
      }

      // Create data points
      const dataPoints = weights.map((weight, index) => [
        dateLabels[index],
        weight,
      ]);

      // Update the series data
      options.series[0].data = dataPoints;
      options.title.text =
        "Bodyweight Trend Since " +
        new Date(dateLabels[0]).toLocaleDateString("en-US", {
          year: "numeric",
          month: "long",
          day: "numeric",
        });
      graphVisible.value = true;
    };

    const generateDateLabels = (count, startDate) => {
      const dates = [];
      for (let i = 0; i < count; i++) {
        let date = new Date(startDate);
        date.setDate(date.getDate() + i);
        dates.push(date.getTime()); // Keep the dates as timestamps
      }
      return dates;
    };

    return {
      weightInput,
      startDate,
      options,
      graphVisible,
      generateGraph,
    };
  },
});
</script>

