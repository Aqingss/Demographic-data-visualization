<template>
  <div ref="chart" style="width: 100%; height: 600px;"></div>
</template>

<script>
import * as echarts from 'echarts';
import urban_rural_population from '@/assets/urban_rural.json';

export default {
  mounted() {
    this.drawChart();
  },
  methods: {
    drawChart() {
      const chartDom = this.$refs.chart;
      const myChart = echarts.init(chartDom);
      const option = {
        title: {
          text: '1960 - 2020年中国城市与农村人口占比变化',
          left: 'center',
          top: 0,
          textStyle: {
            fontSize: 30,
          },
          subtextStyle: {
            fontSize: 18,
          }
        },
        xAxis: {
          type: 'category',
          data: urban_rural_population.map(item => item.Year),
          axisLabel: {
            fontSize: 16,
          }
        },
        yAxis: {
          type: 'value',
          name: '人口百分比（0 - 100%）',
          axisLabel: {
            fontSize: 16,
          },
          min: 0, // 新增：设置 y 轴最小值为 0
          max: 100, // 新增：设置 y 轴最大值为 100
        },
        series: [
          {
            name: 'Urban Population',
            type: 'bar',
            stack: 'population',
            data: urban_rural_population.map(item =>
              item['Urban Population % of Total Population'] === 'Null'? 0 : parseFloat(item['Urban Population % of Total Population'])
            ),
            itemStyle: {
              color: '#FF9900', // 城市人口颜色改为橙色
            },
            label: {
              show: false,
            },
          },
          {
            name: 'Rural Population',
            type: 'bar',
            stack: 'population',
            data: urban_rural_population.map(item =>
              item['Rural Population % of Total Population'] === 'Null'? 0 : parseFloat(item['Rural Population % of Total Population'])
            ),
            itemStyle: {
              color: '#0099FF', // 农村人口颜色改为浅蓝色
            },
            label: {
              show: false, // 不显示数据标签
            },
          },
        ],
        legend: {
          data: ['城市人口', '农村人口'],
          top: 'bottom', // 图例放置在底部
        },
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            type: 'shadow',
          },
          formatter(params) {
            let urban = params[0].value;
            let rural = params[1].value;
            return `城市人口: ${urban}%<br>农村人口: ${rural}%`;
          },
        },
        dataZoom: [
          {
            type: 'slider', // 滑动条类型
            start: 0, // 初始展示的起始位置（0%）
            end: 20, // 初始展示的结束位置（20%）
            handleSize: '80%',
          },
          {
            type: 'inside', // 鼠标滚轮缩放
          },
        ],
      };
      myChart.setOption(option);
    },
  },
};
</script>

<style scoped>
</style>