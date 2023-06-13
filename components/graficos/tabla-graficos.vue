<template>
  <div>
    <v-col>Ventas en el Sistema</v-col>
    <v-col>
      <Bar
        :chart-options="chartOptions"
        :chart-data="chartData"
        :chart-id="chartId"
        :dataset-id-key="datasetIdKey"
        :plugins="plugins"
        :css-classes="cssClasses"
        :styles="styles"
        :width="width"
        :height="height"
      />
    </v-col>
  </div>
</template>

<script>
import { Bar } from 'vue-chartjs/legacy'

import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  BarElement,
  CategoryScale,
  LinearScale
} from 'chart.js'

ChartJS.register(Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale)

export default {
  name: 'BarChart',
  components: {
    Bar
  },
  props: {
    chartId: {
      type: String,
      default: 'bar-chart'
    },
    datasetIdKey: {
      type: String,
      default: 'label'
    },
    width: {
      type: Number,
      default: 500
    },
    height: {
      type: Number,
      default: 400
    },
    cssClasses: {
      default: '',
      type: String
    },
    styles: {
      type: Object,
      default: () => {}
    },
    plugins: {
      type: Array,
      default: () => []
    }
  },
  data () {
    return {
      chartData: {
        labels: [
          'Enero',
          'Febrero',
          'Marzo',
          'Abril',
          'Mayo',
          'Junio',
          'Julio',
          'Agosto',
          'Septiembre',
          'Octubre',
          'Noviembre',
          'Diciembre'
        ],
        datasets: [
          {
            label: '2021',
            backgroundColor: '#0c32c9',
            borderColor: '#fc270f',
            data: [4, 24, 5, 3, 6, 15, 3, 7, 4, 8, 2, 9]
          },
          {
            label: '2022',
            backgroundColor: '#99ebf0',
            borderColor: '#fc270f',
            data: [2, 10, 3, 2, 8, 7, 9, 7, 3, 4, 1, 2]
          },
          {
            label: '2023',
            backgroundColor: '#f0da99',
            borderColor: '#fc270f',
            data: [3, 8, 3, 2, 3, 0, 0, 0, 0, 0, 0, 0]
          }
        ]
      },
      chartOptions: {
        responsive: true,
        maintainAspectRatio: false
      }
    }
  }
}
</script>

<style>
.chart-container {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  grid-gap: 20px;
}

.chart-item {
  border: 1px solid #8f1616;
  padding: 10px;
}
</style>
