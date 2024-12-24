<template>
  <div id="app" style="display: flex; flex-direction: column; height: 100vh;">
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
      selectedProvince: '',
      provinceData: {
        // 模拟每个省份的 985 和 211 数据
        北京: { 985: 8, 211: 26 },
        天津: { 985: 2, 211: 3 },
        河北: { 985: 0, 211: 1 },
        山西: { 985: 0, 211: 1 },
        内蒙古: { 985: 0, 211: 1 },
        辽宁: { 985: 2, 211: 4 },
        吉林: { 985: 1, 211: 3 },
        黑龙江: { 985: 1, 211: 4 },
        上海: { 985: 4, 211: 10 },
        江苏: { 985: 2, 211: 11 },
        浙江: { 985: 1, 211: 1 },
        安徽: { 985: 1, 211: 3 },
        福建: { 985: 1, 211: 2 },
        江西: { 985: 0, 211: 1 },
        山东: { 985: 2, 211: 3 },
        河南: { 985: 0, 211: 1 },
        湖北: { 985: 2, 211: 7 },
        湖南: { 985: 3, 211: 4 },
        广东: { 985: 2, 211: 4 },
        广西: { 985: 0, 211: 1 },
        海南: { 985: 0, 211: 1 },
        重庆: { 985: 1, 211: 2 },
        四川: { 985: 2, 211: 5 },
        贵州: { 985: 0, 211: 1 },
        云南: { 985: 0, 211: 1 },
        西藏: { 985: 0, 211: 1 },
        陕西: { 985: 3, 211: 8 },
        甘肃: { 985: 1, 211: 1 },
        青海: { 985: 0, 211: 1 },
        宁夏: { 985: 0, 211: 1 },
        新疆: { 985: 0, 211: 3 },
        // 可根据实际数据扩展...
      }
    };
  },
  mounted() {
    this.initMap();
    this.initMap1();
    this.initChart();
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
      tooltip: {
        trigger: 'item',
        formatter: '{b}<br/>高校数量: {c}'
      },
      visualMap: {
        min: 0,
        max: 50, // 根据高校数量设置最大值
        left: 'left',
        top: 'bottom',
        text: ['多', '少'], // 文本说明
        calculable: true,
        inRange: {
          color: ['#e0ffff', '#006edd'] // 颜色渐变范围
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
    },
    initMap() {
    this.mapChartInstance = echarts.init(document.getElementById('left-map'));

    const chinaMap = require('echarts/map/json/china.json');
    echarts.registerMap('china', chinaMap);

    const gaokaoData = {
     2020: [
        { name: '全国', value: 1071 },
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
        { name: '全国', value: 1078 },
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
    };

    // 时间轴年份
    const years = Object.keys(gaokaoData);

    const option = {
      baseOption: {
        timeline: {
          axisType: 'category',
          autoPlay: false,
          playInterval: 3000, // 时间间隔
          data: years,
        },
        tooltip: {
          trigger: 'item',
          formatter: '{b}<br/>高考人数: {c}'
        },
        visualMap: {
          min: 50000,
          max: 800000,
          left: 'left',
          top: 'bottom',
          text: ['高', '低'],
          calculable: true,
          inRange: {
            color: ['#e0ffff', '#006edd']
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
            data: gaokaoData[year]
          }
        ]
      }))
    };

    this.mapChartInstance.setOption(option);

      this.mapChartInstance.on('click', (params) => {
        if (params.componentType === 'series') {
          this.fetchProvinceData(params.name);
        }
      });

    this.mapChartInstance.on('timelinechanged', (event) => {
      const currentYearIndex = event.currentIndex; // 获取当前时间轴索引
      const currentYear = years[currentYearIndex];
      console.log(`切换到年份: ${currentYear}`);

      // 手动更新series中的数据
      const currentYearData = gaokaoData[currentYear];

      // 更新图表数据，确保颜色发生变化
      this.mapChartInstance.setOption({
        series: [
          {
            data: currentYearData
          }
        ]
      });
    });
  },
    initChart() {
      this.chartInstance = echarts.init(document.getElementById('chart-container'));

      const option = {
        title: {
          text: '省份数量图表'
        },
        tooltip: {},
        legend: {
          data: ['985', '211'], // 图例展示985和211
          type: 'plain',
          top: '5%' ,// 图例位置
          selectedMode: 'multiple', // 支持多选或单选
        },
        xAxis: {
          type: 'category',
          data: [] // 省份名称
        },
        yAxis: {
          type: 'value',
          max: 30
        },
        series: [
          {
            name: '985',
            type: 'bar',
            data: [] // 填充985数据
          },
          {
            name: '211',
            type: 'bar',
            data: [] // 填充211数据
          }
        ]
      };

      this.chartInstance.setOption(option);
    },
    fetchProvinceData(provinceName) {
      // 从数据中获取省份对应的985和211信息
      const provinceInfo = this.provinceData[provinceName] || { 985: 0, 211: 0 };

      const option = this.chartInstance.getOption();
      option.xAxis[0].data = [provinceName];
      option.series[0].data = [provinceInfo['985']];
      option.series[1].data = [provinceInfo['211']];
      this.selectedProvince = provinceName;

      this.chartInstance.setOption(option);
    }
  }
};
</script>
