<template>
  <div id="app" class="app-container">
    <!--大标题 -->
    <div class="title">
      <h1>基于 第七次普查 与 1960-2020年人口数据 的多维可视化分析</h1>
    </div>

    <!-- 主容器 -->
    <div class="main-container">
      <!-- 左侧展示部分 -->
      <div class="left-sidebar">
        <div class="country-box-container">
          <CountryBox />
        </div>

        <div class="city-rose-container">
          <CityRose :province="currentProvince" />
        </div>

        <div class="gender-pyramid-container">
          <GenderPyramid :province="currentProvince" />
        </div>

        <div class="country-pie-container">
          <CountryPie :province="currentProvince" :city="currentCity" />
        </div>


      </div>

      <!-- 地图和其他表图容器 -->
      <div class="map-and-all-container">
        <div class="map-container">
          <Map @province-selected="handleProvinceSelected" @city-selected="handleCitySelected" />
        </div>

        <div class="changetrend-container">
          <changetrend />
        </div>

        <div class="zhexian-container">
          <zhexian />
        </div>


        <div class="urban-rural-container">
          <urban_rural />
        </div>

        <div class="GDP-expectlife-container">
          <div class="gdp-expectlife-title">1992-2020 年各省人均 GDP 与预期寿命关系</div>
          <GDP_expectlife />
        </div>
      </div>

    </div>
  </div>
</template>

<script>
import Map from './components/Map.vue';
import CityRose from './components/CityRose.vue';
import CountryPie from './components/CountryPie.vue';
import CountryBox from './components/CountryBox.vue';
import zhexian from './components/zhexian.vue';
import changetrend from './components/changetrend.vue';
import GDP_expectlife from './components/GDP_expectlife.vue';
import urban_rural from './components/urban_rural.vue';
import GenderPyramid from './components/GenderPyramid.vue';

export default {
  components: {
    Map,
    CityRose,
    CountryPie,
    CountryBox,
    zhexian,
    changetrend,
    GDP_expectlife,
    urban_rural,
    GenderPyramid
  },
  data() {
    return {
      currentProvince: null,
      currentCity: null
    };
  },
  methods: {
    handleProvinceSelected(province) {
      this.currentProvince = province;
    },
    handleCitySelected(payload) {
      this.currentProvince = payload.province;
      this.currentCity = payload.city;
    }
  }
};
</script>

<style scoped>
.app-container {
  display: flex;
  flex-direction: column;
  height: 200vh;
}

.title {
  text-align: center;
  padding: 20px;
  background-color: #f4f4f4;
}

.title h1 {
  font-size: 24px; /* 减小字体大小 */
  margin: 0;
  color: #333; /* 调整颜色 */
}
.main-container {
  display: flex;
  flex: 1;
  justify-content: space-between;
}

.left-sidebar {
  display: flex;
  flex-direction: column;
  gap: 30px;
  flex: 2; /* 进一步增大左侧宽度比例 */
  max-height: 1480px;
  max-width: 700px; /* 增加最大宽度限制以确保图表数据正常显示 */
  margin-right: 20px;
  overflow-y: auto;
}

.map-and-all-container {
  flex: 1;
  display: flex;
  flex-direction: column;
  height: 100%;
  margin: 0 20px; /* 为地图容器添加左右间距 */
  border: 1px solid #ccc;
}


.map-and-all-container > * {
  flex: 1;
  margin-bottom: 50px; /* 可选：容器之间的间距 */
}

.country-box-container,
.city-rose-container,
.country-pie-container,
.gender-pyramid-container{
  flex: 1;
}

.gdp-expectlife-title {
  text-align: center;
  font-size: 30px;
  margin-bottom: 0px; /* 减小边距 */
}
</style>
