<template>
  <div class="chart-container">
      <div ref="chart" style="height: 400px;"></div>
  </div>
</template>

<script>
import * as echarts from 'echarts';
import countryPopulation from '@/assets/country_population.json';

export default {
  props: {
    province: {
      type: String,
      required: true
    },
    city: {
      type: String,
      required: true
    }
  },
  watch: {
    province(newProvince) {
      this.renderChart(newProvince, this.city);
    },
    city(newCity) {
      this.renderChart(this.province, newCity);
    }
  },
  mounted() {
    this.renderChart(this.province, this.city);
  },
  methods: {
    renderChart(province, city) {
      const countyData = countryPopulation[province] && countryPopulation[province][city] || {};
      const counties = Object.keys(countyData);
      const populations = counties.map(county => countyData[county]);

      const sortedCountiesAndPopulations = counties
       .map((county, index) => ({
          name: county,
          value: populations[index]
        }))
       .sort((a, b) => b.value - a.value);

      const chart = echarts.init(this.$refs.chart);
      const option = {
        title: {
          text: `${city} 各县/区人口分布`,
          left: 'center',
          textStyle: {
            fontSize: 30,
          }
        },
        tooltip: {
          trigger: 'item'
        },
        series: [
          {
            name: '县/区人口',
            type: 'pie',
            radius: '50%',
            data: sortedCountiesAndPopulations,
            emphasis: {
                    itemStyle: {
                      shadowBlur: 10,
                      shadowOffsetX: 0,
                      shadowColor: 'rgba(0, 0, 0, 0.5)'
                    }
            },
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
