<template>
  <div id="app" style="width: 400px">
    <button @click="shuffleData">Shuffle</button>
    <BarChart v-bind="barChartProps" />
  </div>
</template>

<script lang="ts">
import { Chart, ChartData, registerables } from 'chart.js';
import { BarChart, ExtractComponentData } from './dist';
import { useBarChart } from './dist/hooks.js';
import { ref, computed, defineComponent } from 'vue';
import { shuffle } from 'lodash';

Chart.register(...registerables);

export default defineComponent({
  name: 'App',
  props: {
    test: { type: String },
  },
  components: {
    BarChart,
  },
  setup() {
    const data = ref([30, 40, 60, 70, 5]);
    const legendTop = ref(true);

    const options = computed(() => ({
      scales: {
        myScale: {
          type: 'logarithmic',
          position: legendTop.value ? 'left' : 'right',
        },
      },
      plugins: {
        legend: {
          position: legendTop.value ? 'top' : 'bottom',
        },
        title: {
          display: true,
          text: 'Chart.js Doughnut Chart',
        },
      },
    }));

    const testData = computed<ChartData<'bar'>>(() => ({
      labels: ['Paris', 'Nîmes', 'Toulon', 'Perpignan', 'Autre'],
      datasets: [
        {
          data: data.value,
          backgroundColor: ['#77CEFF', '#0079AF', '#123E6B', '#97B0C4', '#A5C8ED'],
        },
      ],
    }));

    const { barChartProps, chartInstance } = useBarChart({
      chartData: testData,
      options: options,
    });

    function shuffleData() {
      data.value = shuffle(data.value);
      legendTop.value = !legendTop.value;
      console.log(chartInstance.value);
    }

    return { data, shuffleData, barChartProps };
  },
});
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
