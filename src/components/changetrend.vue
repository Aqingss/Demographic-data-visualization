<template>
  <div style="display: flex; flex-direction: column; position: relative;">
    <div ref="chart" style="width: 100%; height: 600px;"></div>
    <div style="display: flex; justify-content: flex-end;">
      <button @click="replayChart" class="replay-btn">再次播放</button>
    </div>
  </div>
</template>

<script>
import * as echarts from 'echarts';
import populationData from '@/assets/changetrend.json';

export default {
  data() {
    return {
      chart: null, // 用于存储 Echarts 实例
      option: null // 用于存储图表配置项
    };
  },
  mounted() {
    this.initChart();
  },
  methods: {
    initChart() {
      const chartDom = this.$refs.chart;
      const myChart = echarts.init(chartDom);
      this.chart = myChart;

      // 处理人口数据，去除逗号并转换为数字
      const populations = populationData.map(item => {
        return parseInt(item.Population.replace(/,/g, ''));
      });

      // 提取人口增长率数据，去除百分号并转换为数字
      const increaseRates = populationData.map(item => {
        return parseFloat(item['% Increase in Population'].replace('%', ''));
      });

      const years = populationData.map(item => item.Year);

      const option = {
        title: {
          text: '人口总数及人口增长率随时间变化趋势',
          left: 'center',
          textStyle: {
            fontSize: 30,
          },
          subtextStyle: {
            fontSize: 18,
          }
        },
        xAxis: {
          type: 'category',
          data: years,
          axisLabel: {
            fontSize: 16,
          }
        },
        yAxis: [
          {
            type: 'value',
            name: '人口数量',
            position: 'left',
            axisLabel: {
              fontSize: 16,
            }
          },
          {
            type: 'value',
            name: '人口增长率',
            position: 'right',
            axisLabel: {
              fontSize: 16,
            }
          }
        ],
        series: [
          {
            name: '人口数量',
            type: 'bar',
            data: populations,
            itemStyle: {
              color: '#3399FF'
            },
            label: {
              show: false
            },
            // 添加动画配置，让柱状图渐入显示
            animationDelay: function (idx) {
              return idx * 100;
            }
          },
          {
            name: '人口增长率',
            type: 'line',
            yAxisIndex: 1,
            data: increaseRates,
            itemStyle: {
              color: '#FF9900'
            },
            // 添加动画配置，让折线图渐入显示
            animationDelay: function (idx) {
              return (idx + populations.length) * 100;
            }
          }
        ],
        legend: {
          data: ['人口数量', '人口增长率'],
          top: 'bottom',
          textStyle: {
            fontSize: 20
          }
        },
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            type: 'shadow'
          },
          formatter(params) {
            let tooltip = '';
            params.forEach((item) => {
              if (item.seriesName === 'Population') {
                tooltip += `${item.seriesName}: ${item.value}<br>`;
              } else {
                tooltip += `${item.seriesName}: ${item.value}%<br>`;
              }
            });
            return tooltip;
          }
        }
      };

      this.option = option;
      myChart.setOption(option);
    },
    replayChart() {
      // 清除当前图表实例
      this.chart.clear();
      // 重新设置图表配置项，触发动画重新播放
      this.chart.setOption(this.option);
    }
  }
};
</script>

<style scoped>
.replay-btn {
  padding: 10px 20px;
  background-color: #007BFF;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-size: 16px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
  transition: background-color 0.3s ease;
  margin-right: 100px; /* 新增右侧外边距，将按钮向左推 */
}
.replay-btn:hover {
  background-color: #0056b3;
}
</style>