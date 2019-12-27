<template>
  <div class="dashboard-editor-container">
    <el-row style="background:#fff;margin-bottom:32px;padding-top:10px;">
        <div class="chart-wrapper">
          <h3>line</h3>
          <line-chart :chart-data="lineChartData" />
        </div>
    </el-row>
    <el-row style="background:#fff;margin-bottom:32px;padding-top:10px;">
        <div class="chart-wrapper">
          <h3>line</h3>
          <line-chart :chart-data="lineData" />
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
export default {
  name: 'DashboardAdmin',
  components: {
    LineChart,
    PieChart
  },
  data() {
    return {
      lineChartData: {data:[],legend:[]},
      lineData: {data:[],legend:[]},
      pieData: [],
      data:""
    }
  },
  created() {
    axios.get('./frameData1.txt').then(res=> {
      this.data = JSON.parse(res.data.split('LIGANG:')[1])
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
                symbolSize:'8',
                showSymbol:false,
                data: []
            }
          let obj2 ={
                type: 'line',
                name:key,
                smooth: false,
                sampling:'max',
                symbolSize:'8',
                showSymbol:false,
                data: []
            }          
          obj.data = JSON.parse(this.data[key])
          obj2.data = JSON.parse(this.data[key])
          this.lineChartData.data.push(obj)
          this.lineData.data.push(obj2)
          this.lineData.legend.push(key)
          legend.push(key)
          // for (let z = 0; z < this.data[key].length; z++) {
          //   xAxis.push(this.data[key][z][0])
          // }
        }
        if (key.split('-')[1]==='line') {
          let obj ={
                type: 'line',
                name:key,
                symbolSize: 5,
                data: []
            }
          obj.data = JSON.parse(this.data[key])
          this.lineChartData.data.push(obj)
           legend.push(key)
          // for (let j = 0; j < this.data[key].length; j++) {
          //   xAxis.push(this.data[key][j][0])
          // }
        }
        if (key.split('-')[1]==='pie') {
          let obj2 = {
                name:key,
                legend:[],
                data: [], 
          }
          obj2.data = JSON.parse(this.data[key].data);
          obj2.legend = this.data[key].legend.replace(/\[|\]|\"/g, "").split(',');
          this.pieData.push(obj2)
        }
      }
      // console.log(this.lineChartData)
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
  background-color: #f2f2f2;
  position: relative;

  .github-corner {
    position: absolute;
    top: 0px;
    border: 0;
    right: 0;
  }

  .chart-wrapper {
    background: #fff;
    padding: 16px 16px 0;
    margin-bottom: 32px;
  }
}
</style>
