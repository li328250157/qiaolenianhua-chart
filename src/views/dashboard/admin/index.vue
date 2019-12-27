<template>
  <div class="dashboard-editor-container">
    <el-row style="background:rgb(41, 52, 65);margin-bottom:32px;padding-top:10px;">
        <div class="chart-wrapper">
          <h3>line</h3>
          <line-chart :chart-data="lineChartData" name="SM/FPS/SF" />
        </div>
    </el-row>
    <el-row style="background:rgb(41, 52, 65);margin-bottom:32px;padding-top:10px;">
        <div class="chart-wrapper">
          <h3>line</h3>
          <line-chart :chart-data="lineData" name="SF" />
        </div>
    </el-row>
    <el-row style="background:rgb(41, 52, 65);margin-bottom:32px;padding-top:10px;">
        <div class="chart-wrapper">
          <h3>line</h3>
          <line-chart :chart-data="lineChartData2" name="每一帧耗时" />
        </div>
    </el-row>
    <el-row :gutter="32" v-for="item in pieData" :key="item.id">
        <div class="chart-wrapper">
          <h3>pie</h3>
          <pie-chart :chart-data="item"  />
        </div>
    </el-row>
  </div>
</template>

<script>
import LineChart from './components/LineChart'
import PieChart from './components/PieChart'
import axios from 'axios'
const d3 = require('d3-dsv')
export default {
  name: 'DashboardAdmin',
  components: {
    LineChart,
    PieChart
  },
  data() {
    return {
      lineChartData: {data:[],legend:[]},
      lineChartData2: {data:[],legend:[]},
      lineData: {data:[],legend:[]},
      pieData: [],
      data:""
    }
  },
  created() {
    axios.get('./frameData.txt').then(res=> {
      axios.get('./test.csv').then(res=> {
        let data = d3.csvParse(res.data)
          let obj ={
                type: 'line',
                name:'FPS',
                smooth: false,
                sampling:'max',
                symbolSize:'6',
                showSymbol:false,
                data: []
            } 
         for (let o = 0; o < data.length; o++) {
           let point = []
           point.push(data[o].time);
           point.push(data[o].fps);
           obj.data.push(point)
         }
         this.lineChartData.data.push(obj)
      })
      this.data = JSON.parse(res.data.split('LIGANG:')[1])
      console.log(this.data)
      let legend = []
      let pieLegend = []
      let xAxis = []
      for (const key in this.data) {
        if (key.split('-')[1]==='scatter') {
          let obj ={
                type: 'scatter',
                name:key,
                smooth: false,
                sampling:'max',
                symbolSize:'6',
                showSymbol:false,
                data: []
            }
          let obj2 ={
                type: 'line',
                name:key,
                smooth: false,
                sampling:'max',
                symbolSize:'6',
                showSymbol:false,
                data: [],
                markLine: {
                    data: [
                        {name: '标准线',yAxis:30}
                    ]
                }
            }          
          obj.data = JSON.parse(this.data[key])
          obj2.data = JSON.parse(this.data[key])
          this.lineChartData.data.push(obj)
          this.lineData.data.push(obj2)
          this.lineData.legend.push(key)
          legend.push(key)
        }
        if (key.split('-')[1]==='line') {
          let obj ={
                type: 'line',
                name:key,
                symbolSize: 5,
                data: [],
                markLine: {
                    data: [
                        {name: '标准线',yAxis:30}
                    ]
                }
            }
          obj.data = JSON.parse(this.data[key])
          this.lineChartData.data.push(obj)
           legend.push(key)
        }
        if (key.split('-')[1]==='line2') {
          let obj ={
                type: 'line',
                name:key,
                smooth: false,
                sampling:'max',
                symbolSize:'6',
                showSymbol:false,
                data: [],
                markPoint: {
                    symbolSize:30,
                    data: []
                },
            }
          obj.data = JSON.parse(this.data[key])
          let jank = JSON.parse(this.data.allPerfdogJankLevel)
          for (let p = 0; p < jank.length; p++) {
            let item ={
              value:'',
              xAxis:0,
              yAxis:0
            }
            item.value = jank[p].flag;
            item.xAxis = JSON.parse(jank[p].data)[0];
            item.yAxis = JSON.parse(jank[p].data)[1];
            obj.markPoint.data.push(item)
          }
          this.lineChartData2.data.push(obj)
          this.lineChartData2.legend.push(key)
        }
        if (key.split('-')[1]==='pie') {
          let obj2 = {
                name:key,
                legend:[],
                data: [], 
          }
          if (!!this.data[key].qapmDropTotalCount&&this.data[key].qapmRate&&this.data[key].qapmDuration) {
            obj2.qapmRate = this.data[key].qapmRate;
            obj2.qapmDropTotalCount = JSON.parse(this.data[key].qapmDropTotalCount)[0][1];
            obj2.qapmDuration = JSON.parse(this.data[key].qapmDuration)[0][1]; 
            obj2.text=                        {
                            type: 'text',
                            z: 100,
                            left: '200',
                            top: '20',
                            style: {
                                fill: '#fff',
                                text: [
                                    '流畅率：'+ obj2.qapmRate*10000/100+"%",
                                    '总掉帧数：'+obj2.qapmDropTotalCount,
                                    '绘制时间：'+obj2.qapmDuration+"(ms)",
                                ].join('\n'),
                                font: '14px Microsoft YaHei'
                            }
                        }
          }
          obj2.data = JSON.parse(this.data[key].data);
          obj2.legend = this.data[key].legend.replace(/\[|\]|\"/g, "").split(',');
          this.pieData.push(obj2)
        }
      }
      // console.log(this.lineChartData)
      legend.push('FPS')
      this.lineChartData.legend = legend
      this.lineChartData.xAxis = xAxis
    })
  },
  methods: {

  }
}
</script>

<style lang="scss" scoped>
.dashboard-editor-container {
  padding: 32px;
  background-color: rgb(41, 52, 65);
  position: relative;

  .github-corner {
    position: absolute;
    top: 0px;
    border: 0;
    right: 0;
  }
  h3{
    color: #fff;
  }
  .chart-wrapper {
    background: rgb(41, 52, 65);
    padding: 16px 16px 0;
    margin-bottom: 32px;
  }
}
</style>
