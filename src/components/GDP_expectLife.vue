<template>
  <div ref="chartContainer" style="width: 100%; height: 800px; position: relative; z-index: 0;"></div>
</template>

<script>
import * as echarts from "echarts";
import data from "@/assets/GDP_ExpectLife.json";

export default {
  name: "GdpLifeExpectancyChart",
  mounted() {
    this.initChart();
  },
  methods: {
    initChart() {
      const chartDom = this.$refs.chartContainer;
      const myChart = echarts.init(chartDom);

      // 定义颜色映射，为每个省分配不同的颜色
      const colorPalette = [
        "#5470c6",
        "#91cc75",
        "#fac858",
        "#ee6666",
        "#73c0de",
        "#3ba272",
        "#fc8452",
        "#9a60b4",
        "#ea7ccc",
      ];

      // 提取年份数据（正序）
      const years = Array.from(
        new Set(
          data
         .filter((item) => item.Year >= 1992 && item.Year <= 2020)
         .map((item) => item.Year)
        )
      ).sort((a, b) => a - b);

      // 创建省份与颜色映射
      const provinceColorMap = {};
      data.forEach((item) => {
        Object.keys(item).forEach((key, idx) => {
          if (key!== "Year" && key!== "TotalPopulation" &&!provinceColorMap[key]) {
            provinceColorMap[key] = colorPalette[idx % colorPalette.length];
          }
        });
      });

      // 配置图表
      const option = {
        title: {
          text: "1992-2020 年各省人均 GDP 与预期寿命关系",
          left: "center",
          top: "0%", // 修改为 0%，使标题靠近顶部
          textStyle: {
            fontSize: 18, 
            fontWeight: "bold",
            color: "#333", 
            // 尝试添加 z-index 以避免被遮挡
            zIndex: 10 
          },
        },
        baseOption: {
          timeline: {
            data: years.map((year) => String(year)), 
            axisType: "category",
            autoPlay: true,
            playInterval: 1000,
            orient: "horizontal", 
            bottom: "5%", 
            left: "10%",
            right: "10%",
            label: {
              formatter: "{value}",
            },
            tooltip: {
              formatter: function (params) {
                return `年份：${params.name}`;
              },
            },
          },
          tooltip: {
            trigger: "item",
            formatter: function (params) {
              const province = params.name;
              const gdp = params.value[0];
              const lifeExpectancy = params.value[1];
              return `省份：${province}<br>人均GDP：${gdp} 万元<br>预期寿命：${lifeExpectancy} 岁`;
            },
          },
          grid: {
            // 适当调整 grid 的 top 值
            top: "10%", // 可根据实际情况调整
            bottom: "20%",
            left: "10%",
            right: "10%",
          },
          xAxis: {
            type: "value",
            name: "人均GDP",
            axisLabel: {
              formatter: "{value} 万元",
            },
          },
          yAxis: {
            type: "value",
            name: "预期寿命",
            min: 60,
            max: 80,
            axisLabel: {
              formatter: "{value} 岁",
            },
          },
          visualMap: {
            show: true,
            min: 0,
            max: Object.keys(provinceColorMap).length - 1,
            dimension: 0,
            inRange: {
              color: colorPalette,
            },
            text: [" ", "不同颜色代表不同省份"],
            calculable: true,
            orient: "vertical",
            right: "0%",
            top: "center",
          },
          series: [
            {
              type: "scatter",
              symbolSize: 20, 
              data: [],
            },
          ],
        },
        options: [],
      };

      // 为每个年份构造数据
      years.forEach((year) => {
        const yearData = [];
        data.forEach((item) => {
          if (item.Year === year) {
            Object.keys(item).forEach((key) => {
              if (key!== "Year" && key!== "TotalPopulation") {
                yearData.push({
                  name: key,
                  value: [item[key], item["Life Expectancy"]],
                  itemStyle: {
                    color: provinceColorMap[key],
                  },
                });
              }
            });
          }
        });

        // 将每年的数据添加到 options
        option.options.push({
          series: [
            {
              data: yearData,
            },
          ],
        });
      });

      // 设置图表配置
      myChart.setOption(option);
    },
  },
};
</script>

<style scoped>
/* 样式定义 */
</style>