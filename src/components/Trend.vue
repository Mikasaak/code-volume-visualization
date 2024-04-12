<template>
  <div class="com-container">
    <div class="title" :style="comStyle">
      <span>{{ '▎ ' +  showTitle }}</span>
      <span class="iconfont title-icon" :style="comStyle"  @click="showChoice = !showChoice">&#xe6eb;</span>
      <div class="select-con" v-show="showChoice" :style="marginStyle">
        <div class="select-item" v-for="item in selectTypes" :key="item.key" @click="handleSelect(item.key)">
          {{ item.text }}
        </div>
      </div>
    </div>
    <div class="com-chart" ref="trend_ref"></div>
  </div>
</template>

<script>
import { mapState } from 'vuex'
import { getThemeValue } from '@/utils/theme_utils'
export default {
  data () {
    return {
      chartInstane: null,
      // allData: null, // 从服务器中获取的所有数据
      showChoice: false, // 是否显示可选项
      choiceType: 'map', // 显示的数据类型
      titleFontSize: 0, // 指明标题的字体大小
      allData: {
        map: {
          title: '地区销量趋势',
          base: 310,
          unit: '万',
          data: [
            {
              name: '上海',
              data: [
                '155.13',
                '154.65',
                '171.46',
                '164.38',
                '237.23',
                '300.65',
                '240.29',
                '232.07',
                '193.31',
                '136.70',
                '48.64',
                '90.20'
              ]
            },
            {
              name: '北京',
              data: [
                '86.25',
                '33.80',
                '145.58',
                '21.79',
                '176.09',
                '132.41',
                '291.05',
                '191.89',
                '151.54',
                '94.25',
                '141.75',
                '157.14'
              ]
            },
            {
              name: '深圳',
              data: [
                '143.94',
                '186.29',
                '183.64',
                '251.48',
                '195.48',
                '152.16',
                '52.47',
                '184.12',
                '203.79',
                '39.16',
                '56.37',
                '161.64'
              ]
            },
            {
              name: '广州',
              data: [
                '57.60',
                '77.61',
                '307.24',
                '165.05',
                '175.41',
                '276.88',
                '269.04',
                '296.11',
                '105.31',
                '283.39',
                '134.08',
                '265.38'
              ]
            },
            {
              name: '重庆',
              data: [
                '200.82',
                '215.56',
                '249.80',
                '222.67',
                '216.98',
                '60.12',
                '309.68',
                '273.35',
                '150.99',
                '251.97',
                '26.15',
                '186.99'
              ]
            }
          ]
        },
        seller: {
          title: '商家销量趋势',
          base: 120,
          unit: '万',
          data: [
            {
              name: '商家1',
              data: [
                '33.00',
                '86.07',
                '28.77',
                '34.29',
                '102.45',
                '0.30',
                '50.50',
                '21.70',
                '25.41',
                '25.71',
                '66.90',
                '63.29'
              ]
            },
            {
              name: '商家2',
              data: [
                '12.83',
                '102.42',
                '37.37',
                '95.55',
                '45.45',
                '112.72',
                '113.53',
                '106.41',
                '75.67',
                '113.91',
                '37.32',
                '28.04'
              ]
            },
            {
              name: '商家3',
              data: [
                '73.54',
                '40.92',
                '89.81',
                '113.41',
                '76.34',
                '107.15',
                '55.61',
                '0.33',
                '106.29',
                '78.30',
                '98.05',
                '38.67'
              ]
            },
            {
              name: '商家4',
              data: [
                '47.19',
                '73.57',
                '44.60',
                '84.03',
                '62.82',
                '15.65',
                '64.72',
                '88.98',
                '29.25',
                '5.41',
                '79.11',
                '118.46'
              ]
            },
            {
              name: '商家5',
              data: [
                '74.84',
                '116.45',
                '107.69',
                '11.03',
                '17.31',
                '42.22',
                '97.60',
                '108.64',
                '43.87',
                '110.65',
                '5.96',
                '38.41'
              ]
            }
          ]
        },
        commodity: {
          title: '商品销量趋势',
          base: 50,
          unit: '万',
          data: [
            {
              name: '女装',
              data: [
                '47.71',
                '13.34',
                '19.30',
                '7.93',
                '41.93',
                '23.01',
                '22.63',
                '26.91',
                '0.62',
                '39.23',
                '48.74',
                '29.48'
              ]
            },
            {
              name: '手机数码',
              data: [
                '46.66',
                '46.52',
                '23.65',
                '1.73',
                '44.26',
                '47.07',
                '17.86',
                '40.20',
                '3.78',
                '31.46',
                '28.01',
                '8.63'
              ]
            },
            {
              name: '男装',
              data: [
                '26.98',
                '30.71',
                '42.59',
                '29.50',
                '26.86',
                '17.65',
                '30.15',
                '15.85',
                '9.28',
                '30.20',
                '32.35',
                '34.46'
              ]
            },
            {
              name: '大家电',
              data: [
                '20.26',
                '46.23',
                '43.84',
                '46.75',
                '28.29',
                '32.36',
                '45.30',
                '16.73',
                '40.40',
                '45.07',
                '29.86',
                '41.92'
              ]
            },
            {
              name: '美妆护肤',
              data: [
                '7.58',
                '23.66',
                '39.78',
                '30.20',
                '25.72',
                '36.20',
                '47.55',
                '35.39',
                '27.85',
                '37.56',
                '16.91',
                '3.91'
              ]
            }
          ]
        },
        common: {
          month: [
            '一月',
            '二月',
            '三月',
            '四月',
            '五月',
            '六月',
            '七月',
            '八月',
            '九月',
            '十月',
            '十一月',
            '十二月'
          ]
        },
        type: [
          {
            key: 'map',
            text: '地区销量趋势'
          },
          {
            key: 'seller',
            text: '商家销量趋势'
          },
          {
            key: 'commodity',
            text: '商品销量趋势'
          }
        ]
      }
    }
  },
  created () {
    // 在组件创建完成之后 进行回调函数的注册
    // this.$socket.registerCallBack('trendData', this.getData)
  },
  mounted () {
    this.initChart()
    this.updateChart()
    // this.getData()
    // 发送数据给服务器, 告诉服务器, 我现在需要数据
    // this.$socket.send({
    //   action: 'getData',
    //   socketType: 'trendData',
    //   chartName: 'trend',
    //   value: ''
    // })

    window.addEventListener('resize', this.screenAdapter)
    this.screenAdapter()
  },
  destroyed () {
    window.removeEventListener('resize', this.screenAdapter)
    // 在组件销毁的时候, 进行回调函数的取消
    // this.$socket.unRegisterCallBack('trendData')
  },
  computed: {
    selectTypes () {
      if (!this.allData) {
        return []
      } else {
        return this.allData.type.filter(item => {
          return item.key !== this.choiceType
        })
      }
    },
    showTitle () {
      if (!this.allData) {
        return ''
      } else {
        return this.allData[this.choiceType].title
      }
    },
    // 设置给标题的样式
    comStyle () {
      return {
        fontSize: this.titleFontSize + 'px',
        color: getThemeValue(this.theme).titleColor
      }
    },
    marginStyle () {
      return {
        marginLeft: this.titleFontSize + 'px'
      }
    },
    ...mapState(['theme'])
  },
  methods: {
    initChart () {
      this.chartInstane = this.$echarts.init(this.$refs.trend_ref, this.theme)
      const initOption = {
        grid: {
          left: '3%',
          top: '35%',
          right: '4%',
          bottom: '1%',
          containLabel: true
        },
        tooltip: {
          trigger: 'axis'
        },
        legend: {
          left: 20,
          top: '15%',
          icon: 'circle'
        },
        xAxis: {
          type: 'category',
          boundaryGap: false
        },
        yAxis: {
          type: 'value'
        }
      }
      this.chartInstane.setOption(initOption)
    },
    // ret 就是服务端发送给客户端的图表的数据
    // getData (ret) {
    //   // await this.$http.get()
    //   // 对allData进行赋值
    //   // const { data: ret } = await this.$http.get('trend')
    //   // this.allData = ret
    //   // console.log(this.allData)
    //   this.updateChart()
    // },
    updateChart () {
      // 半透明的颜色值
      const colorArr1 = [
        'rgba(11, 168, 44, 0.5)',
        'rgba(44, 110, 255, 0.5)',
        'rgba(22, 242, 217, 0.5)',
        'rgba(254, 33, 30, 0.5)',
        'rgba(250, 105, 0, 0.5)'
      ]
      // 全透明的颜色值
      const colorArr2 = [
        'rgba(11, 168, 44, 0)',
        'rgba(44, 110, 255, 0)',
        'rgba(22, 242, 217, 0)',
        'rgba(254, 33, 30, 0)',
        'rgba(250, 105, 0, 0)'
      ]
      // 处理数据
      // 类目轴的数据
      const timeArr = this.allData.common.month
      // y轴的数据 series下的数据
      const valueArr = this.allData[this.choiceType].data
      const seriesArr = valueArr.map((item, index) => {
        return {
          name: item.name,
          type: 'line',
          data: item.data,
          stack: this.choiceType,
          areaStyle: {
            color: new this.$echarts.graphic.LinearGradient(0, 0, 0, 1, [
              {
                offset: 0,
                color: colorArr1[index]
              }, // %0的颜色值
              {
                offset: 1,
                color: colorArr2[index]
              } // 100%的颜色值
            ])
          }
        }
      })
      // 图例的数据
      const legendArr = valueArr.map(item => {
        return item.name
      })
      const dataOption = {
        xAxis: {
          data: timeArr
        },
        legend: {
          data: legendArr
        },
        series: seriesArr
      }
      this.chartInstane.setOption(dataOption)
    },
    screenAdapter () {
      this.titleFontSize = this.$refs.trend_ref.offsetWidth / 100 * 3.6
      const adapterOption = {
        legend: {
          itemWidth: this.titleFontSize,
          itemHeight: this.titleFontSize,
          itemGap: this.titleFontSize,
          textStyle: {
            fontSize: this.titleFontSize / 2
          }
        }
      }
      this.chartInstane.setOption(adapterOption)
      this.chartInstane.resize()
    },
    handleSelect (currentType) {
      this.choiceType = currentType
      this.updateChart()
      this.showChoice = false
    }
  },
  watch: {
    theme () {
      console.log('主题切换了')
      this.chartInstane.dispose() // 销毁当前的图表
      this.initChart() // 重新以最新的主题名称初始化图表对象
      this.screenAdapter() // 完成屏幕的适配
      this.updateChart() // 更新图表的展示
    }
  }
}
</script>

<style lang="less" scoped>
.title {
  position: absolute;
  left: 20px;
  top: 20px;
  z-index: 10;
  color: white;
  .title-icon {
    margin-left: 10px;
    cursor: pointer;
  }
  .select-con {
    background-color: #222733;
  }
}
</style>
