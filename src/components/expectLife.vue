<template>
  <div ref="chart" style="width: 100%; height: 600px;"></div>
</template>

<script>
import * as echarts from 'echarts';
import expectLifeData from '@/assets/expectLife.json';

export default {
  mounted() {
    this.drawChart();
  },
  methods: {
    drawChart() {
      const chartDom = this.$refs.chart;
      const myChart = echarts.init(chartDom);

      const years = expectLifeData.map(item => item.Year);
      const lifeExpectancyData = expectLifeData.map(item => parseFloat(item['Life Expectancy']));
      const increasePercentages = expectLifeData.map(item => parseFloat(item['% Increase in Life Expectancy'].replace('%', '')));

      const lifeExpectancyIncrease = [];
      for (let i = 1; i < lifeExpectancyData.length; i++) {
        lifeExpectancyIncrease.push(lifeExpectancyData[i] - lifeExpectancyData[i - 1]);
      }
      lifeExpectancyIncrease.unshift(0); // 第一年的增加量为0

      const option = {
        title: {
          text: '预期寿命增长情况',
          left: 'center'
        },
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            type: 'cross'
          }
        },
        xAxis: {
          type: 'category',
          data: years
        },
        yAxis: {
          type: 'value'
        },
        series: [
          {
            name: '预期寿命增加量',
            type: 'line',
            stack: '总量',
            areaStyle: {
              opacity: 0.5
            },
            data: lifeExpectancyIncrease
          },
          {
            name: '预期寿命增加百分比',
            type: 'line',
            stack: '总量',
            areaStyle: {
              opacity: 0.3,
              color: 'green'
            },
            data: increasePercentages
          }
        ]
      };

      myChart.setOption(option);
    }
  }
};
</script>

<style scoped>
</style>
