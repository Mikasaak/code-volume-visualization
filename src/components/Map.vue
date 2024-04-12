<!-- 商家分布图表 -->
<template>
  <div class='com-container' @dblclick="revertMap">
    <div class='com-chart' ref='map_ref'></div>
  </div>
</template>

<script>
import { mapState } from 'vuex'
import axios from 'axios'
import { getProvinceMapInfo } from '@/utils/map_utils'
export default {
  data () {
    return {
      chartInstance: null,
      allData: null,
      mapData: {}, // 所获取的省份的地图矢量数据
      userCityCount: [
        { name: '北京', value: 100 },
        { name: '天津', value: 200 },
        { name: '上海', value: 300 },
        { name: '重庆', value: 400 },
        { name: '河北', value: 500 },
        { name: '河南', value: 600 },
        { name: '云南', value: 700 },
        { name: '辽宁', value: 800 },
        { name: '黑龙江', value: 900 },
        { name: '湖南', value: 1000 },
        { name: '安徽', value: 1100 },
        { name: '山东', value: 1200 },
        { name: '新疆', value: 1300 },
        { name: '江苏', value: 1400 },
        { name: '浙江', value: 1500 },
        { name: '江西', value: 1600 },
        { name: '湖北', value: 1700 },
        { name: '广西', value: 1800 },
        { name: '甘肃', value: 1900 },
        { name: '山西', value: 2000 },
        { name: '内蒙古', value: 2100 },
        { name: '陕西', value: 2200 },
        { name: '吉林', value: 2300 },
        { name: '福建', value: 2400 },
        { name: '贵州', value: 2500 },
        { name: '广东', value: 2600 },
        { name: '青海', value: 2700 },
        { name: '西藏', value: 2800 },
        { name: '四川', value: 2900 },
        { name: '宁夏', value: 3000 },
        { name: '海南', value: 3100 },
        { name: '台湾', value: 3200 },
        { name: '香港', value: 3300 },
        { name: '澳门', value: 3400 }

      ]
    }
  },
  created () {
    // 在组件创建完成之后 进行回调函数的注册
  },
  mounted () {
    this.initChart()
    // this.getData()
    window.addEventListener('resize', this.screenAdapter)
    this.screenAdapter()
  },
  destroyed () {
    window.removeEventListener('resize', this.screenAdapter)
  },
  methods: {
    async initChart () {
      this.chartInstance = this.$echarts.init(this.$refs.map_ref, this.theme)
      // 获取中国地图的矢量数据
      // http://localhost:8999/static/map/china.json
      // 由于我们现在获取的地图矢量数据并不是位于KOA2的后台, 所以咱们不能使用this.$http
      const ret = await axios.get('http://localhost:8999/static/map/china.json')
      this.$echarts.registerMap('china', ret.data)
      const initOption = {
        title: {
          text: '▎ 商家分布',
          left: 20,
          top: 20
        },
        tooltip: {
          show: true,
          trigger: 'item'
        },
        visualMap: {
          min: 0,
          max: 4000,
          text: ['High', 'Low'],
          realtime: false,
          calculable: true,
          inRange: {
            color: ['lightskyblue', 'yellow', 'orangered']
          }
        },
        // geo: {
        //   type: 'map',
        //   map: 'china',
        //   top: '5%',
        //   bottom: '5%',
        //   itemStyle: {
        //     areaColor: '#2E72BF',
        //     borderColor: '#333'
        //   }
        // },
        legend: {
          show: false,
          left: '5%',
          bottom: '5%',
          orient: 'vertical'
        },
        series: [{
          name: '用户分布',
          type: 'map',
          map: 'china',
          top: '5%',
          bottom: '5%',
          // itemStyle: {
          //   areaColor: '#2E72BF',
          //   borderColor: '#333'
          // },
          label: {
            show: true,
            fontStyle: 'italic',
            color: '#000'
          },
          data: this.userCityCount
        }]
      }
      this.chartInstance.setOption(initOption)
      this.chartInstance.on('click', async arg => {
        // arg.name 得到所点击的省份, 这个省份他是中文
        const provinceInfo = getProvinceMapInfo(arg.name)
        console.log(provinceInfo)
        // 需要获取这个省份的地图矢量数据
        // 判断当前所点击的这个省份的地图矢量数据在mapData中是否存在
        if (!this.mapData[provinceInfo.key]) {
          const ret = await axios.get('http://localhost:8999' + provinceInfo.path)
          this.mapData[provinceInfo.key] = ret.data
          this.$echarts.registerMap(provinceInfo.key, ret.data)
        }
        const changeOption = {
          series: [{
            name: '省份内分布占比',
            type: 'map',
            map: provinceInfo.key,
            top: '5%',
            bottom: '5%',
            itemStyle: {
              areaColor: '#2E72BF',
              borderColor: '#333'
            },
            label: {
              show: true
            },
            data: [
            ]
          }]
          // geo: {
          //   map: provinceInfo.key
          // }
        }
        this.chartInstance.setOption(changeOption)
      })
    },
    updateChart () {
      // 处理图表需要的数据
      // 图例的数据
      const legendArr = this.allData.map(item => {
        return item.name
      })
      const seriesArr = this.allData.map(item => {
        // return的这个对象就代表的是一个类别下的所有散点数据
        // 如果想在地图中显示散点的数据, 我们需要给散点的图表增加一个配置, coordinateSystem:geo
        return {
          type: 'effectScatter',
          rippleEffect: {
            scale: 5,
            brushType: 'stroke'
          },
          name: item.name,
          data: item.children,
          coordinateSystem: 'geo'
        }
      })
      const dataOption = {
        legend: {
          data: legendArr
        },
        series: seriesArr
      }
      this.chartInstance.setOption(dataOption)
    },
    screenAdapter () {
      const titleFontSize = this.$refs.map_ref.offsetWidth / 100 * 3.6
      const adapterOption = {
        title: {
          textStyle: {
            fontSize: titleFontSize
          }
        },
        legend: {
          itemWidth: titleFontSize / 2,
          itemHeight: titleFontSize / 2,
          itemGap: titleFontSize / 2,
          textStyle: {
            fontSize: titleFontSize / 2
          }
        }
      }
      this.chartInstance.setOption(adapterOption)
      this.chartInstance.resize()
    },
    // 回到中国地图
    revertMap () {
      const revertOption = {
        series: [{
          map: 'china',
          data: this.userCityCount
        }]
        // geo: {
        //   map: 'china'
        // }
      }
      this.chartInstance.setOption(revertOption)
    }
  },
  computed: {
    ...mapState(['theme'])
  },
  watch: {
    theme () {
      console.log('主题切换了')
      this.chartInstance.dispose() // 销毁当前的图表
      this.initChart() // 重新以最新的主题名称初始化图表对象
      this.screenAdapter() // 完成屏幕的适配
      this.updateChart() // 更新图表的展示
    }
  }
}
</script>

<style lang='less' scoped>
</style>
