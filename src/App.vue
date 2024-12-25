<template>
  <div id="app" style="display: flex; flex-direction: column; height: 100vh;">
    <div style="position: absolute; top: 50px; right: 200px; display: flex; flex-direction: column; align-items: center; z-index: 1000;">
      <button class="map-button" @click="setMapData('total')">所有高校</button>
      <button class="map-button" @click="setMapData('985')">985高校</button>
      <button class="map-button" @click="setMapData('211')">211高校</button>
      <button class="map-button" @click="setMapData('bk')">本科</button>
      <button class="map-button" @click="setMapData('zk')">专科</button>
    </div>
    <!-- 上层布局，包含左上和右上的地图 -->
    <div style="display: flex; flex: 1;">
      <!-- 左上地图 -->
      <div id="left-map" style="flex: 1; height: 100%;"></div>
      <!-- 右上地图 -->
      <div id="right-map" style="flex: 1; height: 100%;"></div>
    </div>
    <!-- 下层布局，包含左下图表 -->
    <div style="flex: 1; display: flex;">
      <!-- 左下图表 -->
      <div id="chart-container" style="flex: 1; height: 100%;"></div>
      <div id="line-chart-container" style="flex: 1; height: 100%;"></div>
      <div id="pie-chart-container" style="flex: 2; height: 100%;"></div>
    </div>
  </div>
</template>


<script>
import * as echarts from 'echarts';

export default {
  data() {
    return {
      chartInstance: null,
      mapChartInstance: null,
      mapChartInstance1: null,
      lineChartInstance: null,
      pieChart: null,
      selectedProvince: '',
      provinceData: {
        北京: { 985: 8, 211: 26, total: 92 , bk: 67, zk: 25},
        天津: { 985: 2, 211: 3, total: 56  , bk: 30, zk: 26},
        河北: { 985: 0, 211: 1, total: 124 , bk: 61, zk: 63},
        山西: { 985: 0, 211: 1, total: 82  , bk: 34, zk: 48},
        内蒙古: { 985: 0, 211: 1, total: 54, bk: 17, zk: 37},
        辽宁: { 985: 2, 211: 4, total: 114 , bk: 63, zk: 51},
        吉林: { 985: 1, 211: 3, total: 66  , bk: 37, zk: 29},
        黑龙江: { 985: 1, 211: 4, total: 78, bk: 39, zk: 39},
        上海: { 985: 4, 211: 10, total: 64 , bk: 40, zk: 24},
        江苏: { 985: 2, 211: 11, total: 168, bk: 78, zk: 90},
        浙江: { 985: 1, 211: 1, total: 109 , bk: 60, zk: 49},
        安徽: { 985: 1, 211: 3, total: 121 , bk: 46, zk: 75},
        福建: { 985: 1, 211: 2, total: 89  , bk: 39, zk: 50},
        江西: { 985: 0, 211: 1, total: 106 , bk: 45, zk: 61},
        山东: { 985: 2, 211: 3, total: 153 , bk: 70, zk: 83},
        河南: { 985: 0, 211: 1, total: 156 , bk: 57, zk: 99},
        湖北: { 985: 2, 211: 7, total: 130 , bk: 68, zk: 62},
        湖南: { 985: 3, 211: 4, total: 130 , bk: 52, zk: 78},
        广东: { 985: 2, 211: 4, total: 160 , bk: 67, zk: 93},
        广西: { 985: 0, 211: 1, total: 85  , bk: 38, zk: 47},
        海南: { 985: 0, 211: 1, total: 21  , bk: 8 , zk: 13},
        重庆: { 985: 1, 211: 2, total: 70  , bk: 26, zk: 44},
        四川: { 985: 2, 211: 5, total: 134 , bk: 53, zk: 81},
        贵州: { 985: 0, 211: 1, total: 75  , bk: 29, zk: 46},
        云南: { 985: 0, 211: 1, total: 82  , bk: 32, zk: 50},
        西藏: { 985: 0, 211: 1, total: 7   , bk: 4 , zk: 3},
        陕西: { 985: 3, 211: 8, total: 97  , bk: 57, zk: 40},
        甘肃: { 985: 1, 211: 1, total: 49  , bk: 22, zk: 27},
        青海: { 985: 0, 211: 1, total: 12  , bk: 4 , zk: 8},
        宁夏: { 985: 0, 211: 1, total: 20  , bk: 8 , zk: 12},
        新疆: { 985: 0, 211: 3, total: 55  , bk: 19, zk: 36},
      },
      gaokaoData: {
        2020: [
            { name: '安徽', value: 52.38 },
            { name: '北京', value: 5.0 },
            { name: '福建', value: 20.26 },
            { name: '甘肃', value: 26.31 },
            { name: '广东', value: 78.8 },
            { name: '广西', value: 50.7 },
            { name: '贵州', value: 47.06 },
            { name: '海南', value: 5.73 },
            { name: '河北', value: 62.5 },
            { name: '河南', value: 115.8 },
            { name: '黑龙江', value: 21.1 },
            { name: '湖北', value: 39.48 },
            { name: '湖南', value: 53.7 },
            { name: '吉林', value: 15.03 },
            { name: '江苏', value: 34.89 },
            { name: '江西', value: 46.2 },
            { name: '辽宁', value: 19.7 },
            { name: '内蒙古', value: 19.79 },
            { name: '宁夏', value: 6.02 },
            { name: '青海', value: 4.66 },
            { name: '山东', value: 76.93 },
            { name: '山西', value: 32.6 },
            { name: '陕西', value: 32.23 },
            { name: '上海', value: 5.0 },
            { name: '四川', value: 67.47 },
            { name: '天津', value: 5.6 },
            { name: '西藏', value: 3.3 },
            { name: '新疆', value: 22.93 },
            { name: '云南', value: 34.37 },
            { name: '浙江', value: 32.57 },
            { name: '重庆', value: 28.3 }
          ],
          2021: [
            { name: '安徽', value: 54.3 },
            { name: '北京', value: 5.17 },
            { name: '福建', value: 20.1 },
            { name: '甘肃', value: 24.59 },
            { name: '广东', value: 78.3 },
            { name: '广西', value: 55.04 },
            { name: '贵州', value: 46.67 },
            { name: '海南', value: 5.98 },
            { name: '河北', value: 63.4 },
            { name: '河南', value: 125 },
            { name: '黑龙江', value: 19.4 },
            { name: '湖北', value: 40.5 },
            { name: '湖南', value: 57.49 },
            { name: '吉林', value: 15.24 },
            { name: '江苏', value: 35.9 },
            { name: '江西', value: 49.3 },
            { name: '辽宁', value: 19.1 },
            { name: '内蒙古', value: 18.47 },
            { name: '宁夏', value: 8.13 },
            { name: '青海', value: 4.83 },
            { name: '山东', value: 79.5 },
            { name: '山西', value: 31.57 },
            { name: '陕西', value: 31.29 },
            { name: '上海', value: 7 },
            { name: '四川', value: 69.8 },
            { name: '天津', value: 5.6 },
            { name: '西藏', value: 4.1 },
            { name: '新疆', value: 20.98 },
            { name: '云南', value: 35.8 },
            { name: '浙江', value: 33.24 },
            { name: '重庆', value: 28.95 }
          ],
          2022: [
            { name: '北京', value: 5.4 },
            { name: '天津', value: 5.8 },
            { name: '河北', value: 75.32 },
            { name: '山西', value: 33.7 },
            { name: '内蒙古', value: 18.5 },
            { name: '辽宁', value: 24.5 },
            { name: '吉林', value: 12.5 },
            { name: '黑龙江', value: 18 },
            { name: '江西', value: 49.42 },
            { name: '山东', value: 86.7 },
            { name: '上海', value: 5 },
            { name: '江苏', value: 40.6 },
            { name: '浙江', value: 36 },
            { name: '安徽', value: 60.1 },
            { name: '福建', value: 21.8 },
            { name: '河南', value: 125 },
            { name: '湖北', value: 46 },
            { name: '湖南', value: 65.5 },
            { name: '广东', value: 70.2 },
            { name: '广西', value: 61 },
            { name: '海南', value: 6.3 },
            { name: '陕西', value: 32.3 },
            { name: '甘肃', value: 24.32 },
            { name: '宁夏', value: 6.57 },
            { name: '新疆', value: 21.85 },
            { name: '青海', value: 4.84 },
            { name: '重庆', value: 31.4 },
            { name: '四川', value: 57.56 },
            { name: '贵州', value: 47.8 },
            { name: '云南', value: 38.83 },
            { name: '西藏', value: 3.2 }
          ],
          2023: [
            { name: '河南', value: 131 },
            { name: '山东', value: 80 },
            { name: '河北', value: 83 },
            { name: '四川', value: 77 },
            { name: '广东', value: 70.2 },
            { name: '湖南', value: 65.5 },
            { name: '广西', value: 61 },
            { name: '安徽', value: 60.1 },
            { name: '江西', value: 57 },
            { name: '贵州', value: 47.8 },
            { name: '湖北', value: 46 },
            { name: '江苏', value: 40.6 },
            { name: '云南', value: 38.3 },
            { name: '浙江', value: 36 },
            { name: '山西', value: 33.7 },
            { name: '陕西', value: 32.3 },
            { name: '重庆', value: 31.4 },
            { name: '辽宁', value: 19.1 },
            { name: '新疆', value: 22 },
            { name: '甘肃', value: 24.7 },
            { name: '福建', value: 21.8 },
            { name: '内蒙古', value: 18.5 },
            { name: '黑龙江', value: 18.2 },
            { name: '吉林', value: 12.5 },
            { name: '宁夏', value: 7.8 },
            { name: '天津', value: 6.8 },
            { name: '北京', value: 5.4 },
            { name: '上海', value: 5.1 },
            { name: '青海', value: 5.6 },
            { name: '海南', value: 6.3 },
            { name: '西藏', value: 3.2 }
          ],
          2024: [
            { name: '河南', value: 136 },
            { name: '山东', value: 100 },
            { name: '河北', value: 88 },
            { name: '四川', value: 83 },
            { name: '广东', value: 76 },
            { name: '湖南', value: 70 },
            { name: '安徽', value: 67 },
            { name: '江西', value: 65 },
            { name: '湖北', value: 52 },
            { name: '贵州', value: 50.6 },
            { name: '江苏', value: 48 },
            { name: '广西', value: 48 },
            { name: '云南', value: 40.6 },
            { name: '浙江', value: 40.5 },
            { name: '山西', value: 35.4 },
            { name: '陕西', value: 35 },
            { name: '重庆', value: 35 },
            { name: '甘肃', value: 26 },
            { name: '福建', value: 24.7 },
            { name: '新疆', value: 23 },
            { name: '内蒙古', value: 23 },
            { name: '黑龙江', value: 21.1 },
            { name: '辽宁', value: 20.3 },
            { name: '吉林', value: 13.2 },
            { name: '宁夏', value: 8.2 },
            { name: '天津', value: 7.4 },
            { name: '海南', value: 7.3 },
            { name: '北京', value: 6.8 },
            { name: '青海', value: 5.9 },
            { name: '上海', value: 5.8 },
            { name: '西藏', value: 3.9 }
          ]
      },
      tmp: ''
    };
  },
  mounted() {
    this.initMap();
    this.initMap1();
    this.initChart();
    this.initPieChart();
    this.initLineChart();
  },
  methods: {
    initMap1() {
      this.mapChartInstance1 = echarts.init(document.getElementById('right-map'));
      const chinaMap = require('echarts/map/json/china.json');
      echarts.registerMap('china', chinaMap);

      // 高校数量数据
    const universityData = [
      { name: '北京', value: 92 },
      { name: '天津', value: 56 },
      { name: '河北', value: 124},
      { name: '山西', value: 82},
      { name: '内蒙古', value: 54},
      { name: '辽宁', value: 114},
      { name: '吉林', value: 66},
      { name: '黑龙江', value: 78},
      { name: '上海', value: 64 },
      { name: '江苏', value: 168},
      { name: '浙江', value: 109},
      { name: '安徽', value: 121},
      { name: '福建', value: 89},
      { name: '江西', value: 106},
      { name: '山东', value: 153},
      { name: '河南', value: 156},
      { name: '湖北', value: 130},
      { name: '湖南', value: 130},
      { name: '广东', value: 160},
      { name: '广西', value: 85},
      { name: '海南', value: 21},
      { name: '重庆', value: 70},
      { name: '四川', value: 134},
      { name: '贵州', value: 75},
      { name: '云南', value: 82},
      { name: '西藏', value: 7},
      { name: '陕西', value: 97},
      { name: '甘肃', value: 49},
      { name: '青海', value: 12},
      { name: '宁夏', value: 20},
      { name: '新疆', value: 55},
    ];

    // 配置项
    const option = {
      title: {
          text: '中国高校数量分布',  // 标题文本
          left: 'center',  // 标题居中
          top: 30,         // 标题距离顶部的距离
          textStyle: {
            fontSize: 20,  // 字体大小
            fontWeight: 'bold', // 字体加粗
            color: '#333'  // 字体颜色
          }
        },
      tooltip: {
        trigger: 'item',
        formatter: '{b}<br/>高校数量: {c}'
      },
      visualMap: {
        min: 0,
        max: 200, // 根据高校数量设置最大值
        left: 'left',
        top: 'bottom',
        text: ['多', '少'], // 文本说明
        calculable: true,
        inRange: {
          color: ['#e0ffff', '#ff6e00'] // 颜色渐变范围
        }
      },
      series: [
        {
          name: '高校数量',
          type: 'map',
          mapType: 'china', // 指定中国地图
          roam: true, // 是否可缩放、拖拽
          label: {
            show: true,
            formatter: '{b}'
          },
          data: universityData // 数据数组
        }
      ]
    };

      this.mapChartInstance1.setOption(option);
      this.mapChartInstance1.on('click', (params) => {
        if (params.componentType === 'series') {
          if (['香港', '澳门', '台湾', '南海诸岛'].includes(params.name)) {
            console.log(`${params.name} 点击被忽略`);
            return;
          }
          this.updatePieChart(params.name);
        }
    });
    },
    initMap() {
    this.mapChartInstance = echarts.init(document.getElementById('left-map'));

    const chinaMap = require('echarts/map/json/china.json');
    echarts.registerMap('china', chinaMap);

    // 时间轴年份
    const years = Object.keys(this.gaokaoData);

    const option = {
      baseOption: {
        timeline: {
          axisType: 'category',
          autoPlay: false,
          playInterval: 3000, // 时间间隔
          data: years,
        },
        title: {
          text: '中国各省高考人数分布',  // 标题文本
          left: 'center',  // 标题居中
          top: 30,         // 标题距离顶部的距离
          textStyle: {
            fontSize: 20,  // 字体大小
            fontWeight: 'bold', // 字体加粗
            color: '#333'  // 字体颜色
          }
        },
        tooltip: {
          trigger: 'item',
          formatter: '{b}<br/>高考人数: {c}万'
        },
        visualMap: {
          min: 0,
          max: 150,
          left: 'left',
          top: 'bottom',
          text: ['高', '低'],
          calculable: true,
          inRange: {
            color: ['#e0ffe0', '#ff0000']
          }
        },
        series: [
          {
            type: 'map',
            map: 'china',
            label: {
              show: true
            },
            roam: true,
            data: []
          }
        ]
      },
      options: years.map(year => ({
        series: [
          {
            data: this.gaokaoData[year]
          }
        ]
      }))
    };

    this.mapChartInstance.setOption(option);

    this.mapChartInstance.on('click', (params) => {
        if (params.componentType === 'series') {
          if (['香港', '澳门', '台湾', '南海诸岛'].includes(params.name)) {
            console.log(`${params.name} 点击被忽略`);
            return;
          }
          this.updateLineChart(params.name);
        }
    });

    this.mapChartInstance.on('timelinechanged', (event) => {
      const currentYearIndex = event.currentIndex; // 获取当前时间轴索引
      const currentYear = years[currentYearIndex];
      console.log(`切换到年份: ${currentYear}`);

      // 手动更新series中的数据
      const currentYearData = this.gaokaoData[currentYear];

      // 更新图表数据，确保颜色发生变化
      this.mapChartInstance.setOption({
        series: [
          {
            data: currentYearData
          }
        ]
      });
      this.updatePiePeople(currentYear);
    });
  },
    initChart() {
      this.chartInstance = echarts.init(document.getElementById('chart-container'));

      const option = {
        title: {
          text: '各省高考人数占比',
          left: 'center',
          top: 30,
        },
        tooltip: {
          trigger: 'item',
          formatter: '{b}: {c} ({d}%)',
        },
        series: [
          {
            name: '高校类型',
            type: 'pie',
            radius: '50%',
            data: [
            ],
          },
        ],
      };

      this.chartInstance.setOption(option);
      this.updatePiePeople(2020);
    },
    initPieChart() {
      // 初始化饼图
      this.pieChart = echarts.init(document.getElementById('pie-chart-container'));
      
      const option = {
        title: {
          text: '高校类型占比',
          left: 'center',
          top: 30,
        },
        tooltip: {
          trigger: 'item',
          formatter: '{b}: {c} ({d}%)',
        },
        series: [
          {
            name: '高校类型',
            type: 'pie',
            radius: '50%',
            data: [
              { value: 0, name: '985高校' },
              { value: 0, name: '211高校' },
              { value: 0, name: '其他高校' },
            ],
          },
        ],
      };

      this.pieChart.setOption(option);
      this.updatePieChart('北京');
    },
    initLineChart() {
      // Initialize the line chart
      this.lineChartInstance = echarts.init(document.getElementById('line-chart-container'));

      const option = {
        title: {
          text: '省份历年高考人数',
          left: 'center',
          top: 30,
        },
        tooltip: {
          trigger: 'axis',
          formatter: '{b}<br/>{a}: {c}万'
        },
        xAxis: {
          type: 'category',
          data: [], // Years
        },
        yAxis: {
          type: 'value',
          name: '人数 (万)',
        },
        series: [
          {
            name: '高考人数',
            type: 'line',
            data: [], // Data for the selected province
          }
        ]
      };

      this.lineChartInstance.setOption(option);
      this.updateLineChart('北京');
    },
    updateLineChart(provinceName) {
      // Fetch data for the province across years
      const years = Object.keys(this.gaokaoData);
      const data = years.map(year => {
        const yearData = this.gaokaoData[year];
        const provinceData = yearData.find(item => item.name === provinceName);
        return provinceData ? provinceData.value : 0;
      });

      // const rawMinValue = Math.min(...data);
      // const rawMaxValue = Math.max(...data);
      // const minValue = Math.floor(rawMinValue / 10) * 10; // 向下取整到十位
      // const maxValue = Math.ceil(rawMaxValue / 10) * 10;  // 向上取整到十位
      const growthRates = data.map((value, index) => {
        if (index === 0) return 'N/A'; // 第一年没有增长率
        const previousValue = data[index - 1];
        return ((value - previousValue) / previousValue * 100).toFixed(2) + '%';
      });

      const option = {
        title: {
          text: `${provinceName}历年高考人数`,
        },
        xAxis: {
          data: years,
        },
        yAxis: {
          type: 'value',
          name: '人数 (万)',
          min: 0, // 动态设置最小值
          max: 140, // 动态设置最大值
        },
        series: [
          {
            data,
          }
        ]
      };

      this.lineChartInstance.setOption(option);
        // 显示增长率信息
      const growthInfo = years.map((year, index) => {
        return `${year}: ${growthRates[index]}`;
      }).join('<br/>');

      const growthContainer = document.getElementById('growth-rate-container');
      if (!growthContainer) {
        const container = document.createElement('div');
        container.id = 'growth-rate-container';
        container.style.position = 'absolute';
        container.style.top = '20px';
        container.style.right = '10px';
        container.style.padding = '10px';
        container.style.border = '1px solid #ccc';
        container.style.backgroundColor = '#fff';
        container.style.fontSize = '12px';
        container.innerHTML = growthInfo;
        document.getElementById('line-chart-container').appendChild(container);
      } else {
        growthContainer.innerHTML = growthInfo;
      }
    },
    updatePieChart(provinceName) {
      // 更新饼图数据
      const province = this.provinceData[provinceName];

      const option = {
        title: {
          text: `${provinceName}高校类型占比`,
          left: 'center',
        },
        series: [
          {
            data: [
              { value: province['985'], name: '985高校' },
              { value: province['211'] - province['985'], name: '211高校' },
              { value: province['bk'] - province['985'] - province['211'], name: '其他本科' },
              { value: province['zk'], name: '专科' },
            ],
          },
        ],
      };

      this.pieChart.setOption(option);
    },
    updatePiePeople(currentYear){
      const option = {
        title: {
          text: `${currentYear}年各省人数分布`,
          left: 'center',
        },
        series: [
          {
            data: this.gaokaoData[currentYear],
          },
        ],
      };
      this.chartInstance.setOption(option);
    },
    setMapData(type) {
      let data = [];
      let { minVal, maxVal } = this.getExtremes(type);
      switch (type) {
        case '985':
          data = Object.keys(this.provinceData).map(province => ({
            name: province,
            value: this.provinceData[province]['985']
          }));
          break;
        case '211':
          data = Object.keys(this.provinceData).map(province => ({
            name: province,
            value: this.provinceData[province]['211']
          }));
          break;
        case 'bk':
          data = Object.keys(this.provinceData).map(province => ({
            name: province,
            value: this.provinceData[province]['bk']
          }));
          break;
        case 'zk':
          data = Object.keys(this.provinceData).map(province => ({
            name: province,
            value: this.provinceData[province]['zk']
          }));
          break;
        default:
          data = Object.keys(this.provinceData).map(province => ({
            name: province,
            value: this.provinceData[province]['total']
          }));
          maxVal = 200;
      }
      this.mapChartInstance1.setOption({
        series: [{
          data: data
        }],
        visualMap: {
          min: minVal - minVal,
          max: maxVal, // 根据高校数量设置最大值
          left: 'left',
          top: 'bottom',
          text: ['多', '少'], // 文本说明
          calculable: true,
          inRange: {
            color: ['#e0ffff', '#ff6e00'] // 颜色渐变范围
          }
        },
      });
    },
    getExtremes(type) {
      let minVal = Infinity;
      let maxVal = 0;
      const provinces = Object.keys(this.provinceData);

      provinces.forEach(province => {
        const value = this.provinceData[province][type];
        if (value < minVal) minVal = value;
        if (value > maxVal) maxVal = value;
      });

      // 确保最小值和最大值有意义
      minVal = minVal === Infinity ? 0 : minVal;
      maxVal = maxVal === 0 ? 1 : maxVal; // 至少有一个高校

      return { minVal, maxVal };
    },
  }
};
</script>

<style>
.map-button {
  padding: 8px 16px; /* Increased padding for larger buttons */
  margin: 10px 0; /* Increased margin between buttons */
  border: none;
  border-radius: 8px; /* Slightly larger border radius for a softer look */
  background-color: #007bff;
  color: white;
  font-size: 16px; /* Increased font size for better readability */
  cursor: pointer;
  transition: background-color 0.3s ease, transform 0.1s ease;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2); /* Increased box shadow for depth */
  width: 100px; /* Adjusted width for larger buttons */
  text-transform: uppercase; /* Optional: uppercase text for a cleaner look */
  letter-spacing: 0.5px; /* Optional: adjusted letter spacing */
}

.map-button:hover {
  background-color: #0056b3;
  transform: translateY(-2px); /* Lift the button on hover */
}

.map-button:active {
  transform: translateY(2px); /* Press the button down when clicked */
}
</style>