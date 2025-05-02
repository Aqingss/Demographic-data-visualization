<template>
  <div ref="chart" style="width: 100%; height: 600px;"></div>
</template>

<script>
import * as echarts from 'echarts';
import data from '@/assets/zhexian.json';

export default {
  mounted() {
    this.drawChart();
  },
  methods: {
    drawChart() {
      const chartDom = this.$refs.chart;
      const myChart = echarts.init(chartDom);

      // 处理数据
      const years = [];
      const lifeExpectancy = [];
      const birthRate = [];
      const deathRate = [];
      const infantMortalityRate = [];
      const fertilityRate = [];
      const netMigrationRate = [];
      const population = [];

      data.forEach(item => {
        years.push(item.Year);
        lifeExpectancy.push(item['Life Expectancy']);
        birthRate.push(item['Birth Rate']);
        deathRate.push(item['Death Rate']);
        infantMortalityRate.push(item['Infant Mortality Rate']);
        fertilityRate.push(item['Fertility Rate']);
        netMigrationRate.push(item['Net Migration Rate']);
        population.push(item['Population']);
      });

      // 动态计算Y轴的最小值和最大值
      const allData = [
     ...lifeExpectancy,
     ...birthRate,
     ...deathRate,
     ...infantMortalityRate,
     ...fertilityRate,
     ...netMigrationRate
      ];
      let minValue = Math.min(...allData);
      let maxValue = Math.max(...allData);

      // 人口数量数据的最小值和最大值
      const populationData = population;
      let minPopulation = Math.min(...populationData);
      let maxPopulation = Math.max(...populationData);

      // 调整人口数量的最小值和最大值，使其波动更明显
      const populationBuffer = 0.1; // 增加缓冲区比例
      const populationBufferAmount = (maxPopulation - minPopulation) * populationBuffer;
      minPopulation -= populationBufferAmount;
      maxPopulation += populationBufferAmount;

      // 计算合适的刻度间隔
      let range = maxValue - minValue;
      let interval = range / 10;

      // 人口数量的刻度间隔
      let populationRange = maxPopulation - minPopulation;
      let populationInterval = populationRange / 10;

      const option = {
        title: {
          text: '人口相关指标随时间变化趋势',
          left: 'center',
          textStyle: {
            fontSize: 30,
          }
        },
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            type: 'cross'
          },
          formatter: function (params) {
            let tooltipText = '';
            params.forEach(param => {
              tooltipText += `${param.seriesName}: ${param.value}<br>`;
            });
            return tooltipText;
          },
          textStyle: {
            fontSize: 12
          }
        },
        legend: {
          data: ['预期寿命', '出生率', '死亡率', '婴儿死亡率', '生育率', '净迁移率', '人口数量'],
          orient: 'horizontal',
          bottom: 0,
          textStyle: {
            fontSize: 20
          }
        },
        xAxis: {
          type: 'category',
          data: years,
          name: '年份',
          axisLabel: {
            fontSize: 16,
          }
        },
        yAxis: [
          {
            type: 'value',
            name: '指标值',
            min: minValue,
            max: maxValue,
            interval: interval,
            axisLabel: {
              formatter: function (value) {
                return value.toFixed(2);
              }
            },
            axisLabel: {
              fontSize: 16,
            }
          },
          {
            type: 'value',
            name: '人口数量',
            min: 500000000, // 手动设置人口数量Y轴最小值为0
            max: 1450000000, // 手动设置人口数量Y轴最大值
            axisLabel: {
              formatter: function (value) {
                return value.toFixed(2);
              }
            },
            axisLabel: {
              fontSize: 16,
            },
            position: 'right'
          }
        ],
        series: [
          {
            name: '预期寿命',
            type: 'line',
            data: lifeExpectancy,
            symbol: 'circle',
            lineStyle: {
              width: 2
            },
            label: {
              show: false
            },
            emphasis: {
              focus: 'series',
              lineStyle: {
                width: 3,
                color:'red'
              }
            }
          },
          {
            name: '出生率',
            type: 'line',
            data: birthRate,
            symbol: 'circle',
            lineStyle: {
              width: 2
            },
            label: {
              show: false
            },
            emphasis: {
              focus: 'series',
              lineStyle: {
                width: 3,
                color:'red'
              }
            }
          },
          {
            name: '死亡率',
            type: 'line',
            data: deathRate,
            symbol: 'circle',
            lineStyle: {
              width: 2
            },
            label: {
              show: false
            },
            emphasis: {
              focus: 'series',
              lineStyle: {
                width: 3,
                color:'red'
              }
            }
          },
          {
            name: '婴儿死亡率',
            type: 'line',
            data: infantMortalityRate,
            symbol: 'circle',
            lineStyle: {
              width: 2
            },
            label: {
              show: false
            },
            emphasis: {
              focus: 'series',
              lineStyle: {
                width: 3,
                color:'red'
              }
            }
          },
          {
            name: '生育率',
            type: 'line',
            data: fertilityRate,
            symbol: 'circle',
            lineStyle: {
              width: 2
            },
            label: {
              show: false
            },
            emphasis: {
              focus: 'series',
              lineStyle: {
                width: 3,
                color:'red'
              }
            }
          },
          {
            name: '净迁移率',
            type: 'line',
            data: netMigrationRate,
            symbol: 'circle',
            lineStyle: {
              width: 2
            },
            label: {
              show: false
            },
            emphasis: {
              focus: 'series',
              lineStyle: {
                width: 3,
                color:'red'
              }
            }
          },
          {
            name: '人口数量',
            type: 'line',
            data: population,
            symbol: 'circle',
            lineStyle: {
              width: 10 // 加粗人口数量线
            },
            label: {
              show: false
            },
            emphasis: {
              focus: 'series',
              lineStyle: {
                width: 3,
                color:'red'
              }
            },
            yAxisIndex: 1
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
