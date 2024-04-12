<!-- 商家销量统计的横向柱状图 -->
<template>
  <div class="com-container">
    <div class="com-chart" ref="seller_ref"></div>
  </div>
</template>

<script>
import { mapState } from 'vuex'
export default {
  data () {
    return {
      chartInstance: null,
      // allData: null, // 服务器返回的数据
      currentPage: 1, // 当前显示的页数
      totalPage: 0, // 一共有多少页
      timerId: null, // 定时器的标识
      allData: [
        { name: '函数数量', value: 100 },
        { name: '变量数量', value: 200 },
        { name: '类数量', value: 300 },
        { name: '接口数量', value: 400 },
        { name: '注释数量', value: 500 },
        { name: '循环数量', value: 600 },
        { name: '条件数量', value: 700 },
        { name: '异常数量', value: 800 },
        { name: '注解数量', value: 900 },
        { name: '包数量', value: 1000 },
        { name: '文件数量', value: 1100 },
        { name: '行数数量', value: 1200 },
        { name: '字符数量', value: 1300 },
        { name: '空行数量', value: 1400 },
        { name: '代码块数量', value: 1500 },
        { name: '代码行数', value: 1600 },
        { name: '代码字符数', value: 1700 },
        { name: '代码空行数', value: 1800 },
        { name: '代码块数', value: 1900 },
        { name: '代码文件数', value: 2000 },
        { name: '代码包数', value: 2100 },
        { name: '代码注解数', value: 2200 },
        { name: '代码异常数', value: 2300 },
        { name: '代码条件数', value: 2400 },
        { name: '代码循环数', value: 2500 },
        { name: '代码注释数', value: 2600 },
        { name: '代码接口数', value: 2700 },
        { name: '代码类数', value: 2800 },
        { name: '代码变量数', value: 2900 },
        { name: '代码函数数', value: 3000 }
      ]

    }
  },
  created () {
    // 在组件创建完成之后 进行回调函数的注册
  },
  mounted () {
    this.initChart()
    this.processData()
    // this.getData()
    window.addEventListener('resize', this.screenAdapter)
    // 在页面加载完成的时候, 主动进行屏幕的适配
    this.screenAdapter()
  },
  destroyed () {
    clearInterval(this.timerId)
    // 在组件销毁的时候, 需要将监听器取消掉
    window.removeEventListener('resize', this.screenAdapter)
  },
  methods: {
    // 初始化echartInstance对象
    initChart () {
      this.chartInstance = this.$echarts.init(this.$refs.seller_ref, this.theme)
      // 对图表初始化配置的控制
      const initOption = {
        title: {
          text: '▎XXXX统计',
          left: 20,
          top: 20
        },
        grid: {
          top: '20%',
          left: '3%',
          right: '6%',
          bottom: '3%',
          containLabel: true // 距离是包含坐标轴上的文字
        },
        xAxis: {
          type: 'value'
        },
        yAxis: {
          type: 'category'
        },
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            type: 'line',
            z: 0,
            lineStyle: {
              color: '#2D3443'
            }
          }
        },
        series: [
          {
            type: 'bar',
            label: {
              show: true,
              position: 'right',
              textStyle: {
                color: 'white'
              }
            },
            itemStyle: {
              // 指明颜色渐变的方向
              // 指明不同百分比之下颜色的值
              color: new this.$echarts.graphic.LinearGradient(0, 0, 1, 0, [
                // 百分之0状态之下的颜色值
                {
                  offset: 0,
                  color: '#5052EE'
                },
                // 百分之100状态之下的颜色值
                {
                  offset: 1,
                  color: '#AB6EE5'
                }
              ])
            }
          }
        ]
      }
      this.chartInstance.setOption(initOption)

      // 对图表对象进行鼠标事件的监听
      this.chartInstance.on('mouseover', () => {
        clearInterval(this.timerId)
      })
      this.chartInstance.on('mouseout', () => {
        this.startInterval()
      })
    },
    // 获取服务器的数据
    processData () {
      // this.allData = ret
      // 对数据排序
      this.allData.sort((a, b) => {
        return a.value - b.value // 从小到大的排序
      })
      // 每5个元素显示一页
      this.totalPage = this.allData.length % 5 === 0 ? this.allData.length / 5 : this.allData.length / 5 + 1
      this.updateChart()
      // 启动定时器
      this.startInterval()
    },

    getData (ret) {
      // this.allData = ret
      // 对数据排序
      this.allData.sort((a, b) => {
        return a.value - b.value // 从小到大的排序
      })
      // 每5个元素显示一页
      this.totalPage = this.allData.length % 5 === 0 ? this.allData.length / 5 : this.allData.length / 5 + 1
      this.updateChart()
      // 启动定时器
      this.startInterval()
    },
    // 更新图表
    updateChart () {
      const start = (this.currentPage - 1) * 5
      const end = this.currentPage * 5
      const showData = this.allData.slice(start, end)
      const names = showData.map((item) => {
        return item.name
      })
      const values = showData.map((item) => {
        return item.value
      })
      const dataOption = {
        yAxis: {
          data: names
        },
        series: [
          {
            data: values
          }
        ]
      }
      this.chartInstance.setOption(dataOption)
    },
    startInterval () {
      if (this.timerId) {
        clearInterval(this.timerId)
      }
      this.timerId = setInterval(() => {
        this.currentPage++
        if (this.currentPage > this.totalPage) {
          this.currentPage = 1
        }
        this.updateChart()
      }, 3000)
    },
    // 当浏览器的大小发生变化的时候, 会调用的方法, 来完成屏幕的适配
    screenAdapter () {
      // console.log(this.$refs.seller_ref.offsetWidth)
      const titleFontSize = this.$refs.seller_ref.offsetWidth / 100 * 3.6
      // 和分辨率大小相关的配置项
      const adapterOption = {
        title: {
          textStyle: {
            fontSize: titleFontSize
          }
        },
        tooltip: {
          axisPointer: {
            lineStyle: {
              width: titleFontSize
            }
          }
        },
        series: [
          {
            barWidth: titleFontSize,
            itemStyle: {
              barBorderRadius: [0, titleFontSize / 2, titleFontSize / 2, 0]
            }
          }
        ]
      }
      this.chartInstance.setOption(adapterOption)
      // 手动的调用图表对象的resize 才能产生效果
      this.chartInstance.resize()
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

<style lang="less" scoped>
</style>
