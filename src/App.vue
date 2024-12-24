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
        上海: { 985: 4, 211: 9 },
        广东: { 985: 2, 211: 6 },
        浙江: { 985: 1, 211: 3 },
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
      { name: '北京', value: 50 },
      { name: '上海', value: 30 },
      { name: '广东', value: 40 },
      { name: '江苏', value: 35 },
      { name: '浙江', value: 20 },
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
      2021: [
        { name: '北京', value: 50000 },
        { name: '上海', value: 60000 },
        { name: '广东', value: 700000 },
        { name: '江苏', value: 800000 },
      ],
      2022: [
        { name: '北京', value: 52000 },
        { name: '上海', value: 61000 },
        { name: '广东', value: 710000 },
        { name: '江苏', value: 810000 },
      ],
      2023: [
        { name: '北京', value: 820000 },
        { name: '上海', value: 62000 },
        { name: '广东', value: 720000 },
        { name: '江苏', value: 50000 },
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
