<template>
  <div :class="className" :style="{height:height,width:width}" />
</template>

<script>
import echarts from 'echarts'
require('echarts/theme/macarons') // echarts theme
import resize from './mixins/resize'

export default {
  mixins: [resize],
  props: {
    className: {
      type: String,
      default: 'chart'
    },
    width: {
      type: String,
      default: '100%'
    },
    height: {
      type: String,
      default: '350px'
    },
    chartData: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      chart: null
    }
  },
  mounted() {
    this.$nextTick(() => {
      this.initChart()
    })
  },
  beforeDestroy() {
    if (!this.chart) {
      return
    }
    this.chart.dispose()
    this.chart = null
  },
  watch: {
    chartData: {
      deep: true,
      handler(val) {
        this.setpieOptions(val)
      }
    }
  },
  methods: {
    initChart() {
      this.chart = echarts.init(this.$el, 'macarons')
      this.setpieOptions(this.chartData)
    },
    setpieOptions(data){
      this.chart.clear()
      let arr = []
      for (let i = 0; i < data.data.length; i++) {
        let obj = {
          value:data.data[i],
          name:data.legend[i]
        }
        data.data[i] = Number(data.data[i])
        arr.push(obj)
      }
        this.chart.setOption({
            title : {
                text: data.name,
                x:'center'
            },
            tooltip : {
                trigger: 'item',
                formatter: "{a} <br/>{b} : {c} ({d}%)"
            },
            legend: {
                orient: 'vertical',
                left: 'left',
                data: data.legend
            },
            graphic: [
                        {
                            type: 'text',
                            z: 100,
                            left: '200',
                            top: 'middle',
                            style: {
                                fill: '#333',
                                text: [
                                    '横轴表示温度，单位是°C',
                                    '纵轴表示高度，单位是km',
                                    '右上角有一个图片做的水印',
                                    '这个文本块可以放在图中各',
                                    '种位置'
                                ].join('\n'),
                                font: '14px Microsoft YaHei'
                            }
                        }
            ],
            series : [
                {
                    name: '访问来源',
                    type: 'pie',
                    radius : '55%',
                    center: ['50%', '60%'],
                    data:arr,
                    itemStyle: {
                        emphasis: {
                            shadowBlur: 10,
                            shadowOffsetX: 0,
                            shadowColor: 'rgba(0, 0, 0, 0.5)'
                        }
                    }
                }
            ]
        })
      },
  }
}
</script>
