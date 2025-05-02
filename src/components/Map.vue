<template>
  <div>
    <div ref="chart" style="height: 800px;"></div>

    <div v-if="currentProvince" class="back-button-container">
      <button @click="goBack">返回上一级</button>
    </div>

    <div v-if="hoveredProvince" class="population-info">
      <p>{{ hoveredProvince }} 总人口: {{ provincePopulation }}</p>
    </div>

    <div class="data-show-container">
      <DataShow />
    </div>
  </div>
</template>

<script>
import * as echarts from 'echarts';
import china from '@/assets/china.json';
import provincePopulation from '@/assets/province_population.json';
import cityPopulation from '@/assets/city_population.json';
import countryPopulation from '@/assets/country_population.json';
import CityRose from './CityRose.vue';
import CountryPie from './CountryPie.vue';

const provinceMapLoader = {
  "安徽": () => import('@/assets/province/anhui.json'),
  "北京": () => import('@/assets/province/beijing.json'),
  "重庆": () => import('@/assets/province/chongqing.json'),
  "福建": () => import('@/assets/province/fujian.json'),
  "甘肃": () => import('@/assets/province/gansu.json'),
  "广东": () => import('@/assets/province/guangdong.json'),
  "广西": () => import('@/assets/province/guangxi.json'),
  "贵州": () => import('@/assets/province/guizhou.json'),
  "海南": () => import('@/assets/province/hainan.json'),
  "河北": () => import('@/assets/province/hebei.json'),
  "黑龙江": () => import('@/assets/province/heilongjiang.json'),
  "河南": () => import('@/assets/province/henan.json'),
  "湖北": () => import('@/assets/province/hubei.json'),
  "湖南": () => import('@/assets/province/hunan.json'),
  "江苏": () => import('@/assets/province/jiangsu.json'),
  "江西": () => import('@/assets/province/jiangxi.json'),
  "吉林": () => import('@/assets/province/jilin.json'),
  "辽宁": () => import('@/assets/province/liaoning.json'),
  "内蒙古": () => import('@/assets/province/neimenggu.json'),
  "宁夏": () => import('@/assets/province/ningxia.json'),
  "青海": () => import('@/assets/province/qinghai.json'),
  "山东": () => import('@/assets/province/shandong.json'),
  "上海": () => import('@/assets/province/shanghai.json'),
  "山西": () => import('@/assets/province/shanxi.json'),
  "四川": () => import('@/assets/province/sichuan.json'),
  "天津": () => import('@/assets/province/tianjin.json'),
  "新疆": () => import('@/assets/province/xinjiang.json'),
  "西藏": () => import('@/assets/province/xizang.json'),
  "云南": () => import('@/assets/province/yunnan.json'),
  "浙江": () => import('@/assets/province/zhejiang.json')
};

const cityMapLoader = {
  "杭州市": () => import('@/assets/cities/330100.json'),
  "宁波市": () => import('@/assets/cities/330200.json'),
  "温州市": () => import('@/assets/cities/330300.json'),
  "嘉兴市": () => import('@/assets/cities/330400.json'),
  "湖州市": () => import('@/assets/cities/330500.json'),
  "绍兴市": () => import('@/assets/cities/330600.json'),
  "金华市": () => import('@/assets/cities/330700.json'),
  "衢州市": () => import('@/assets/cities/330800.json'),
  "舟山市": () => import('@/assets/cities/330900.json'),
  "台州市": () => import('@/assets/cities/331000.json'),
  "丽水市": () => import('@/assets/cities/331100.json')
};

export default {
  data() {
    return {
      chart: null,
      hoveredProvince: null, // 鼠标悬浮的省份
      provincePopulation: null, // 鼠标悬浮的省份总人口
      currentProvince: null, // 当前显示的省份地图
      currentCity: null, // 当前显示的城市
      showCityRose: false,
      showCountryPie: false,
    };
  },
  mounted() {
    this.initChart();
  },
  methods: {
    initChart() {
      this.chart = echarts.init(this.$refs.chart);
      echarts.registerMap('china', china);
      const option = {
        tooltip: {
          trigger: 'item',
          formatter: (params) => {
            const province = params.name;
            const population = this.getPopulation(province);
            return `${province} 总人口: ${population}`;
          },
        },
        visualMap: {
          min: Math.min(...Object.values(provincePopulation)),
          max: Math.max(...Object.values(provincePopulation)),
          left: 'left',
          top: 'bottom',
          show: true,
          dimension: 0,
          text: ['高人口', '低人口'],
          inRange: {
          color: ['#fdda4b', '#dd0003'],
          },
        },
        series: [
          {
            type: 'map',
            map: 'china',
            roam: true,
            label: {
              show: true
            },
            data: Object.keys(provincePopulation).map(province => ({
              name: province,
              value: provincePopulation[province]
            }))
          }
        ]
      };

      this.chart.setOption(option);

      // 监听鼠标悬浮事件
      this.chart.on('mouseover', (params) => {
        const province = params.name;
        if (provincePopulation[province]) {
          this.hoveredProvince = province;
          this.provincePopulation = provincePopulation[province];
        } else {
          this.hoveredProvince = null;
          this.provincePopulation = null;
        }
      });

      // 监听点击事件
      this.chart.on('click', (params) => {
        const province = params.name;
        this.loadProvinceMap(province);
      });
    },

    // 获取省份总人口
    getPopulation(province) {
      if (provincePopulation[province]) {
        return provincePopulation[province];
      }
      return '数据缺失';
    },

    // 获取市总人口
    getcityPopulation(province, city){
      if (cityPopulation[province]) {
          if (cityPopulation[province][city]) {
            return cityPopulation[province][city];
          } else {
            return '数据缺失';
          }
        } else {
          return '数据缺失';
        }
    },

    // 获取县/区总人口
    getcountryPopulation(province, city, country){
      if (countryPopulation[province]) {
          if (countryPopulation[province][city]) {
            if (countryPopulation[province][city][country] !== undefined) {
              return countryPopulation[province][city][country];
            } else {
              return '数据缺失';
            }
          } else {
            return '数据缺失';
          }
        } else {
          return '数据缺失';
      }
    },

    // 加载省份地图
    loadProvinceMap(province) {
      const loader = provinceMapLoader[province];

      if (loader) {
        loader()
          .then((mapJson) => {
            echarts.registerMap(province, mapJson);

            this.chart.resize();

            const option = {
              tooltip: {
                trigger: 'item',
                formatter: (params) => {
                  const city = params.name;
                  const population = this.getcityPopulation(province, city);
                  return `${city} : ${population}`;
                }
              },
              visualMap: {
                min: Math.min(...Object.values(cityPopulation[province] || {})),
                max: Math.max(...Object.values(cityPopulation[province] || {})),
                left: 'left',
                top: 'bottom',
                show: true,
                dimension: 0,
                text: ['高人口', '低人口'],
                inRange: {
                color: ['#fdda4b', '#dd0003'],
                },
              },
              series: [
                {
                  type: 'map',
                  map: province,
                  roam: true,
                  label: {
                    show: true
                  },
                  data: Object.keys(cityPopulation[province] || {}).map(city => ({
                    name: city,
                    value: cityPopulation[province][city]
                    }))
                }
              ]
            };

            this.chart.setOption(option);
            this.$emit('province-selected', province);
            this.currentProvince = province;
            this.showCityRose = true;

            this.chart.on('click', (params) => {
              const province = params.name;
              this.loadCityMap(province);
            });
          })
          .catch((err) => {
            console.error('加载省份地图失败:', err);
          });
      } else {
        console.warn(`没有找到 ${province} 的地图数据`);
      }
    },

    // 加载市地图
    loadCityMap(city) {
      const loader = cityMapLoader[city];
        if (loader) {
          loader().then((mapJson) => {
            echarts.registerMap(city, mapJson);

            this.chart.resize();

            const option = {
              tooltip: {
                trigger: 'item',
                formatter: (params) => {
                  const country = params.name;
                  const population = this.getcountryPopulation(this.currentProvince, city, country);
                  return `${country} : ${population}`;
                }
              },
              visualMap: {
                min: Math.min(...Object.values(countryPopulation[this.currentProvince][city] || {})),
                max: Math.max(...Object.values(countryPopulation[this.currentProvince][city] || {})),
                left: 'left',
                top: 'bottom',
                show: true,
                dimension: 0,
                text: ['高人口', '低人口'],
                inRange: {
                color: ['#fdda4b', '#dd0003'],
                },
              },
              series: [
                {
                  type: 'map',
                  map: city,
                  roam: true,
                  label: {
                    show: true
                  },
                  data: Object.keys(countryPopulation[this.currentProvince][city] || {}).map(county => ({
                    name: county,
                    value: this.getcountryPopulation(this.currentProvince, city, county)
                  }))
                }
              ]
            };

            this.chart.setOption(option);
            this.$emit('city-selected', { province: this.currentProvince, city});
            this.currentCity = city;

            this.showCountryPie = true;
          }).catch((err) => {
            console.error('加载城市地图失败:', err);
          });
         }
      },

    // 返回到上一级地图
    goBack() {
          if (this.currentCity) {
            this.currentCity = null;
            this.loadProvinceMap(this.currentProvince);
          } else if (this.currentProvince) {
            this.currentProvince = null;
            this.initChart();
            this.showCityRose = false;
          }
        }
      },
  beforeDestroy() {
    if (this.chart) {
      this.chart.dispose();
    }
  }
};
</script>

<style scoped>
.population-info {
  margin-top: 20px;
  font-size: 18px;
  font-weight: bold;
  color: #333;
}

.back-button-container {
  margin-top: 20px;
}

button {
  padding: 10px 20px;
  font-size: 16px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

button:hover {
  background-color: #0056b3;
}
</style>
