<template>
  <div>
    <div ref="chart" style="width: 100%; height: 1000px;"></div>
  </div>
</template>

<script>
import * as echarts from 'echarts';
import provincePopulation from '@/assets/province_population.json';

export default {
  data() {
    return {
      sortedData: [],
      chartInstance: null,
    };
  },
  mounted() {
    this.prepareData();
    this.initChart();
    this.animateChart();
  },
  methods: {
    prepareData() {
      this.sortedData = Object.entries(provincePopulation)
        .map(([province, population]) => ({ province, population }))
        .sort((a, b) => a.population - b.population);
    },
    initChart() {
      this.chartInstance = echarts.init(this.$refs.chart);

      const option = {
        title: [
          {
            text: '第七次全国人口普查 - 省份人口动态柱状图',
            left: 'center',
            textStyle: {
              fontSize: 30,
            },
          },
          {
            text: '',
            right: 20,
            top: 20,
            textStyle: {
              fontSize: 25,
              color: '#FFA726',
            },
          },
        ],
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            type: 'shadow',
          },
        },
        xAxis: {
          type: 'value',
          name: '人口数',
          axisLabel: {
            formatter: '{value}',
          },
        },
        yAxis: {
          type: 'category',
          name: '省份',
          data: [],
          inverse: true,
          axisLabel: {
            fontSize: 18,
            color: '#333',
          },
        },
        series: [
          {
            name: '人口',
            type: 'bar',
            data: [],
            itemStyle: {
              color: '#73C0DE',
            },
          },
        ],
      };

      this.chartInstance.setOption(option);
    },
    animateChart() {
      let index = 0;
      const interval = setInterval(() => {
        if (index < this.sortedData.length) {
          const current = this.sortedData.slice(0, index + 1);
          const currentProvince = this.sortedData[index];

          const yAxisData = current.map(item => item.province);
          const seriesData = current.map(item => item.population);

          this.chartInstance.setOption({
            title: [
              { text: '第七次全国人口普查 - 省份人口动态柱状图' },
              {
                text: `当前更新: ${currentProvince.province} - ${currentProvince.population.toLocaleString()} 人`,
              },
            ],
            yAxis: {
              data: yAxisData,
              axisLabel: {
                fontSize: 18,
                color: '#333',
              },
            },
            series: [
              {
                data: seriesData,
              },
            ],
          });

          index++;
        } else {
          clearInterval(interval);
        }
      }, 500);
    },
  },
};
</script>

<style scoped>
/* 画布自适应 */
</style>
