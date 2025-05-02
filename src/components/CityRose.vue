<template>
  <div class="chart-container">
      <div ref="chart" style="height: 400px;"></div>
  </div>
</template>

<script>
import * as echarts from 'echarts';
import cityPopulation from '@/assets/city_population.json';

export default {
  props: {
    province: {
      type: String,
      required: true
    }
  },
  watch: {
    province(newProvince) {
      this.renderChart(newProvince);
    }
  },
  mounted() {
    this.renderChart(this.province);
  },
  methods: {
    renderChart(province) {
      const cityData = cityPopulation[province] || {};
      const cities = Object.keys(cityData);
      const populations = cities.map(city => cityData[city]);

      const sortedCitiesAndPopulations = cities
        .map((city, index) => ({
          name: city,
            value: populations[index]
        }))
        .sort((a, b) => b.value - a.value);

      const chart = echarts.init(this.$refs.chart);
      const option = {
        title: {
          text: `${province} 各市人口分布`,
          left: 'center',
          top:0,
          textStyle: {
            fontSize: 30,
          }
        },
        tooltip: {
          trigger: 'item'
        },
        series: [
          {
            name: '城市人口',
            type: 'pie',
            radius: ['40%', '70%'],
            roseType: 'radius',
            data: sortedCitiesAndPopulations,
            label: {
              fontSize: 20,
            },
            labelLine: {
              length: 10,
              length2: 10,
            }
          }
        ]
      };

      chart.setOption(option);
    }
  }
};
</script>

<style scoped>
</style>
