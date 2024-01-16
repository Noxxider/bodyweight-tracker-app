<template>
  <q-page class="flex flex-center">
    <div class="q-px-md full-width">
      <div
        class="text-h6 text-weight-bold text-center full-width q-mt-xl"
      >
        Bodyweight Tracker
      </div>
      <div v-if="!graphVisible" style="max-width: 400px" class="q-mx-auto q-mt-md q-mb-xl">
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
          hint="Press calendar symbol to choose a start date"
        >
          <template v-slot:append>
            <q-icon name="event" class="cursor-pointer">
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
          class="shadow-1 q-mt-md full-width"
          no-caps
          padding="md lg"
          color="accent"
          @click="generateGraph"
          unelevated
        />
      </div>
      <div
        v-if="graphVisible"
        style="max-width: 800px"
        class="q-mx-auto q-mt-lg"
      >
        <ApexCharts
          style="max-width: 100%"
          :options="options"
          :series="options.series"
        ></ApexCharts>
      </div>

      <div
        v-if="statsVisible"
        style="max-width: 600px"
        class="q-mx-auto q-mt-md q-mb-xl"
      >
        <q-list bordered class="rounded-borders">
          <div class="text-h6 text-weight-bold q-my-md text-center">
            Weight Statistics
          </div>
          <q-item-label class="q-pb-none" header>Average Weight</q-item-label>
          <q-item class="q-pt-none">
            <q-item-section>{{ averageWeight }} kg</q-item-section>
          </q-item>
          <q-item-label class="q-pb-none" header>Net Weight Gain</q-item-label>
          <q-item class="q-pt-none">
            <q-item-section>{{ netWeightGain }} kg</q-item-section>
          </q-item>
          <q-item-label class="q-pb-none" header>Minimum Weight</q-item-label>
          <q-item class="q-pt-none">
            <q-item-section>{{ minWeight }} kg</q-item-section>
          </q-item>
          <q-item-label class="q-pb-none" header>Maximum Weight</q-item-label>
          <q-item class="q-pt-none">
            <q-item-section>{{ maxWeight }} kg</q-item-section>
          </q-item>
          <q-item-label class="q-pb-none" header>Weight Range</q-item-label>
          <q-item class="q-pt-none">
            <q-item-section>{{ weightRange }} kg</q-item-section>
          </q-item>
          <q-item-label class="q-pb-none" header>Average Daily Weight Change</q-item-label>
          <q-item class="q-pt-none">
            <q-item-section>{{ dailyWeightGain }} kg</q-item-section>
          </q-item>
          <q-item class="q-pt-none q-px-md">
            <q-btn
              label="Reset Data"
              type="primary"
              class="shadow-1 q-mb-md full-width"
              no-caps
              outline
              padding="md lg"
              color="accent"
              @click="resetData()"
              unelevated
            />
          </q-item>
        </q-list>
      </div>
    </div>
  </q-page>
</template>

<script>
import { defineComponent, ref, computed } from "vue";
import ApexCharts from "vue3-apexcharts";

export default defineComponent({
  name: "BodyweightTracker",
  components: { ApexCharts },
  setup() {
    var weightInput = ref("");
    var startDate = ref("");
    const graphVisible = ref(false);
    const statsVisible = ref(false);

    // Initialize computed properties with default values
    let averageWeight = ref("0.00");
    let netWeightGain = ref("0.00");
    let minWeight = ref("0.00");
    let maxWeight = ref("0.00");
    let weightRange = ref("0.00");
    let dailyWeightGain = ref("0.00");

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

    const resetData = () => {
      statsVisible.value = false;
      graphVisible.value = false;
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

      // Calculate statistics based on the new data
      if (weights.length > 0) {
        const totalWeight = weights.reduce((acc, weight) => acc + weight, 0);
        averageWeight.value = (totalWeight / weights.length).toFixed(2);
        netWeightGain.value = (weights.at(-1) - weights[0]).toFixed(2);
        minWeight.value = Math.min(...weights).toFixed(2);
        maxWeight.value = Math.max(...weights).toFixed(2);
        weightRange.value = (maxWeight.value - minWeight.value).toFixed(2);
        dailyWeightGain.value = (netWeightGain.value / weights.length).toFixed(
          2
        );
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
      if (weights.length > 0) {
        statsVisible.value = true;
      }
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
      resetData,
      options,
      graphVisible,
      generateGraph,
      statsVisible,
      averageWeight,
      netWeightGain,
      minWeight,
      maxWeight,
      weightRange,
      dailyWeightGain
    };
  },
});
</script>

