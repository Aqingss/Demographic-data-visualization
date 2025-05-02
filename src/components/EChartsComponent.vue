<template>
  <div ref="chart" style="width: 100%; height: 400px;"></div>
</template>

<script>
import * as echarts from 'echarts';

export default {
  mounted() {
    this.initChart();
  },
  methods: {
    initChart() {
      const chartDom = this.$refs.chart;
      const myChart = echarts.init(chartDom);

      const option = {
        title: {
          text: 'ECharts 示例'
        },
        tooltip: {},
        xAxis: {
          data: ['A', 'B', 'C', 'D', 'E', 'F', 'G']
        },
        yAxis: {},
        series: [
          {
            name: '销量',
            type: 'bar',
            data: [10, 52, 200, 334, 390, 330, 220]
          }
        ]
      };

      myChart.setOption(option);
    }
  }
};
</script>

<style>
/* 你可以在这里添加样式 */
</style>
