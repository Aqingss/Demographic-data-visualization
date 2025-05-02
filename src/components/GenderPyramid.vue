<template>
  <div id="population-pyramid" style="width: 100%; height: 500px;"></div>
</template>

<script>
import * as echarts from "echarts";
import gender from "@/assets/gender.json"; // 引入人口 JSON 数据

export default {
  props: {
    province: {
      type: String,
      required: true,
    },
  },
  watch: {
    province: "renderPopulationPyramid", // 监听省份变化，重新渲染图表
  },
  mounted() {
    this.renderPopulationPyramid();
  },
  methods: {
    renderPopulationPyramid() {
      const chartDom = document.getElementById("population-pyramid");
      if (!chartDom) return;

      const chart = echarts.init(chartDom);

      // 获取对应省份的人口数据，若不存在则使用全国数据
      const data = gender[this.province] || gender["全国"];
      const title = data === gender["全国"] ? "全国人口金字塔" : `${this.province}人口金字塔`;

      // 年龄段和对应数据
      const ageRanges = [];
      const maleData = [];
      const femaleData = [];

      for (const ageRange in data) {
        if (ageRange === "合计") continue;
        ageRanges.push(ageRange);
        maleData.push(-data[ageRange].男); // 男性数据为负数，左侧显示
        femaleData.push(data[ageRange].女); // 女性数据为正数，右侧显示
      }

      // 配置 ECharts
      chart.setOption({
        title: {
          text: title,
          left: "center",
          top: 0,
          textStyle: {
            fontSize: 30,
          },
        },
        tooltip: {
          trigger: "axis",
          axisPointer: {
            type: "shadow",
          },
          formatter: (params) => {
            // 格式化鼠标悬浮时显示的内容
            const maleValue = Math.abs(params[0].value); // 转换为正数
            const femaleValue = params[1].value;
            return `
              <div>
                <strong>${params[0].name}</strong><br/>
                男性: ${maleValue} 人<br/>
                女性: ${femaleValue} 人
              </div>
            `;
          },
        },
        legend: {
          data: ["男性", "女性"],
          top: 40,
          textStyle: {
            fontSize: 20,
          },
        },
        grid: {
          left: "0%",
          right: "10%",
          top: "20%",
          bottom: "0%",
          containLabel: true,
        },
        xAxis: {
          type: "value",
          axisLabel: {
            formatter: (value) => Math.abs(value),
          },
        },
        yAxis: {
          type: "category",
          data: ageRanges,
          axisLabel: {
            fontSize: 16,
          },
        },
        series: [
          {
            name: "男性",
            type: "bar",
            data: maleData,
            barWidth: 10,
            itemStyle: {
              color: "#5470C6",
            },
          },
          {
            name: "女性",
            type: "bar",
            data: femaleData,
            barWidth: 10,
            itemStyle: {
              color: "#EE6666",
            },
          },
        ],
      });
    },
  },
};
</script>

<style scoped>
#population-pyramid {
  margin: 20px auto;
}
</style>
