<template>
  <div :class="className" :style="{height:height,width:width}" />
</template>

<script>
import echarts from 'echarts'
require("../components/chalk");  // echarts theme
import resize from './mixins/resize'
import { param } from './mixins';

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
      this.chart = echarts.init(this.$el, 'chalk')
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
              formatter: function (params) {
                console.log(params)
                // let str = '<div><p>' + params[0].axisValue +'</p></div>'
                // for (let i = 0; i < params.length; i++) {
                //   str += params[i].marker + ' ' + params[i].seriesName +':'+params[i].data.name + '<br>'
                // }
                // return str;
              }
            },
            legend: {
                orient: 'vertical',
                left: 'left',
                data: data.legend
            },
            graphic: [data.text],
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
                    },
            label: {
                normal: {
                    formatter: '{a|{a}}{abg|}\n{hr|}\n  {b|{b}：}{c}  {per|{d}%}  ',
                    backgroundColor: '#000',
                    borderColor: '#aaa',
                    borderWidth: 1,
                    borderRadius: 4,
                    // shadowBlur:3,
                    // shadowOffsetX: 2,
                    // shadowOffsetY: 2,
                    // shadowColor: '#999',
                    // padding: [0, 7],
                    rich: {
                        a: {
                            color: '#999',
                            lineHeight: 22,
                            align: 'center'
                        },
                        // abg: {
                        //     backgroundColor: '#333',
                        //     width: '100%',
                        //     align: 'right',
                        //     height: 22,
                        //     borderRadius: [4, 4, 0, 0]
                        // },
                        hr: {
                            borderColor: '#aaa',
                            width: '100%',
                            borderWidth: 0.5,
                            height: 0
                        },
                        b: {
                            fontSize: 16,
                            lineHeight: 33
                        },
                        per: {
                            color: '#eee',
                            backgroundColor: '#334455',
                            padding: [2, 4],
                            borderRadius: 2
                        }
                    }
                }
            }
                }
            ]
        })
      },
  }
}
</script>
