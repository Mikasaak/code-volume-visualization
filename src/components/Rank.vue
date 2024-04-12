<!-- 地区销售排行 -->

<template>
  <div class='com-container'>
    <div class='com-chart' ref='rank_ref'></div>
  </div>
</template>

<script>
import { mapState } from 'vuex'

export default {
  data() {
    return {
      chartInstance: null,
      allData: null,
      startValue: 0, // 区域缩放的起点值
      endValue: 9, // 区域缩放的终点值
      timerId: null // 定时器的标识
    }
  },
  created() {
    // 在组件创建完成之后 进行回调函数的注册
  },
  mounted() {
    this.initChart()
    this.updateChart()
    window.addEventListener('resize', this.screenAdapter)
    this.screenAdapter()
  },
  destroyed() {
    window.removeEventListener('resize', this.screenAdapter)
    clearInterval(this.timerId)
  },
  methods: {
    initChart() {
      this.chartInstance = this.$echarts.init(this.$refs.rank_ref, this.theme)
      const initOption = {
        title: {
          text: '▎ 每日代码提交统计',
          left: 20,
          top: 20
        },
        grid: {
          top: '40%',
          left: '5%',
          right: '5%',
          bottom: '5%',
          containLabel: true
        },
        tooltip: {
          show: true
        },
        xAxis: {
          type: 'category'
        },
        yAxis: {
          type: 'value'
        },
        series: [
          {
            type: 'bar'
          }
        ]
      }
      this.chartInstance.setOption(initOption)
      this.chartInstance.on('mouseover', () => {
        clearInterval(this.timerId)
      })
      this.chartInstance.on('mouseout', () => {
        this.startInterval()
      })
    },
    updateChart() {
      const colorArr = [
        ['#0BA82C', '#4FF778'],
        ['#2E72BF', '#23E5E5'],
        ['#5052EE', '#AB6EE5']
      ]
      const codeSubmitData = [
        {
          date: '2023.1.1',
          codeQuantity: 230
        },
        {
          date: '2023.1.2',
          codeQuantity: 250
        },
        {
          date: '2023.1.3',
          codeQuantity: 280
        },
        {
          date: '2023.1.4',
          codeQuantity: 300
        },
        {
          date: '2023.1.5',
          codeQuantity: 320
        },
        {
          date: '2023.1.6',
          codeQuantity: 340
        },
        {
          date: '2023.1.7',
          codeQuantity: 360
        },
        {
          date: '2023.1.8',
          codeQuantity: 380
        },
        {
          date: '2023.1.9',
          codeQuantity: 400
        },
        {
          date: '2023.1.10',
          codeQuantity: 420
        },
        {
          date: '2023.1.11',
          codeQuantity: 440
        },
        {
          date: '2023.1.12',
          codeQuantity: 460
        },
        {
          date: '2023.1.13',
          codeQuantity: 480
        },
        {
          date: '2023.1.14',
          codeQuantity: 500
        },
        {
          date: '2023.1.15',
          codeQuantity: 520
        },
        {
          date: '2023.1.16',
          codeQuantity: 540
        },
        {
          date: '2023.1.17',
          codeQuantity: 560
        },
        {
          date: '2023.1.18',
          codeQuantity: 580
        },
        {
          date: '2023.1.19',
          codeQuantity: 600
        },
        {
          date: '2023.1.20',
          codeQuantity: 620
        }
      ]

      const dateArr = codeSubmitData.map(item => {
        return item.date
      })
      const codeQuantityArr = codeSubmitData.map(item => {
        return item.codeQuantity
      })
      const dataOption = {
        xAxis: {
          data: dateArr
        },
        dataZoom: {
          show: false,
          startValue: this.startValue,
          endValue: this.endValue
        },
        series: [
          {
            data: codeQuantityArr,
            itemStyle: {
              color: arg => {
                let targetColorArr = null
                if (arg.value > 300) {
                  targetColorArr = colorArr[0]
                } else if (arg.value > 200) {
                  targetColorArr = colorArr[1]
                } else {
                  targetColorArr = colorArr[2]
                }
                return new this.$echarts.graphic.LinearGradient(0, 0, 0, 1, [
                  {
                    offset: 0,
                    color: targetColorArr[0]
                  },
                  {
                    offset: 1,
                    color: targetColorArr[1]
                  }
                ])
              }
            }
          }
        ]
      }
      this.chartInstance.setOption(dataOption)
    },
    screenAdapter() {
      const titleFontSize = this.$refs.rank_ref.offsetWidth / 100 * 3.6
      const adapterOption = {
        title: {
          textStyle: {
            fontSize: titleFontSize
          }
        },
        series: [
          {
            barWidth: titleFontSize,
            itemStyle: {
              barBorderRadius: [titleFontSize / 2, titleFontSize / 2, 0, 0]
            }
          }
        ]
      }
      this.chartInstance.setOption(adapterOption)
      this.chartInstance.resize()
    },
    startInterval() {
      if (this.timerId) {
        clearInterval(this.timerId)
      }
      this.timerId = setInterval(() => {
        this.startValue++
        this.endValue++
        if (this.endValue > this.allData.length - 1) {
          this.startValue = 0
          this.endValue = 9
        }
        this.updateChart()
      }, 2000)
    }
  },
  computed: {
    ...mapState(['theme'])
  },
  watch: {
    theme() {
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
