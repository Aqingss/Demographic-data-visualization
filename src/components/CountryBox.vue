<template>
  <div class="table-wrapper">
    <h2 class="table-title">全国各县/区人口排名</h2>
    <div
      class="table-container"
      @mouseover="stopScroll"
      @mouseleave="resumeScroll"
    >
      <div ref="scrollWrapper" class="scroll-wrapper">
        <table class="population-table">
          <thead>
            <tr>
              <th>排名</th>
              <th>省</th>
              <th>市</th>
              <th>县</th>
              <th>人口数</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(item, index) in sortedData" :key="index">
              <td>{{ index + 1 }}</td>
              <td>{{ item.province }}</td>
              <td>{{ item.city }}</td>
              <td>{{ item.district }}</td>
              <td>{{ item.population.toLocaleString() }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
import * as echarts from 'echarts';
import countryPopulation from '@/assets/country_population.json';

export default {
  data() {
    return {
      sortedData: [],
      isScrolling: true,
    };
  },
  methods: {
    flattenAndSortData() {
      const flattenedData = [];
      for (const province in countryPopulation) {
        const cities = countryPopulation[province];
        for (const city in cities) {
          const districts = cities[city];
          for (const district in districts) {
            flattenedData.push({
              province,
              city,
              district,
              population: districts[district],
            });
          }
        }
      }
      this.sortedData = flattenedData.sort((a, b) => b.population - a.population);
    },
    stopScroll() {
      this.isScrolling = false;
      this.$refs.scrollWrapper.style.animationPlayState = 'paused';
    },
    resumeScroll() {
      this.isScrolling = true;
      this.$refs.scrollWrapper.style.animationPlayState = 'running';
    },
  },
  mounted() {
    this.flattenAndSortData();
  },
};
</script>

<style scoped>
.table-wrapper {
  width: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 20px;
  background-color: #f9f9f9;
  border-radius: 10px;
}

.table-title {
  font-size: 1.8rem;
  margin-bottom: 10px;
  width: fit-content;
  text-align: center;
}

.table-container {
  max-width: 800px;
  max-height: 400px;
  overflow: hidden;
  position: relative;
  width: 100%;
}

.scroll-wrapper {
  display: inline-block;
  animation: scrollTable 3000s linear infinite;
  animation-play-state: running; /* 默认运行状态 */
}

.population-table {
  width: 100%;
  border-collapse: collapse;
}

.population-table th,
.population-table td {
  padding: 8px 16px;
  border: 1px solid #ddd;
  text-align: left;
}

.population-table th {
  background-color: #fafafa;
  position: sticky;
  top: 0;
  z-index: 1;
}

.population-table tr:hover {
  background-color: #e8f5ff;
  transition: background-color 0.3s;
}

@keyframes scrollTable {
  0% {
    transform: translateY(0);
  }
  100% {
    transform: translateY(-100%);
  }
}
</style>
