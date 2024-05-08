<template>
  <div class="hello" style="padding: 10px;">
    <div id="chartstemp" style="height: 500px;width: 100%;"></div>
    <div id="chartsAllCo2" style="height: 500px;width: 100%;"></div>
    <div id="chartsNumCo2" style="height: 200px;width: 100%;"></div>
  </div>
</template>

<script>
import * as echarts from 'echarts'

import temperatureData from '../assets/datas/tempratrue.json'
import co2AllData from '../assets/datas/allco2.json'
import co2NumData from '../assets/datas/numco2.json'

export default {
  name: 'HelloWorld',
  data(){
    return{
      tempData:[],
      co2AllData:[],
      co2NumData:[],
    }
  },
  mounted(){
    // console.log(temperatureData,co2AllData)
    this.co2AllData = co2AllData
    this.co2NumData = co2NumData
    this.tempData = temperatureData.slice(1)

    this.renderChartAllCo2()
    this.renderChartNumCo2()
  },
  methods:{
    renderChartAllCo2(){
      // console.log(co2AllData)
      const dataS=Object.keys(this.co2AllData[0]).filter(key => key !== "Year")
      // console.log(dataS)
      let series=[]
      dataS.forEach((item=>{
        series.push(
          {
            name:item,
            type:"bar",
            stack:"1",
            data:this.co2AllData.map(item2=>(item2[`${item}`])),
          },)
      }))
      
      const myChart = echarts.init(document.getElementById('chartsAllCo2'))
      const option = {
        legend: {
          show:true,
          type:'scroll',
        },
        tooltip:{
          show:true,
          formatter:function(params){
            return `
            <div>
              <div>
                <span>国家或地区：</span>
                <span>${params.seriesName}</span>
              </div>
              <div>
                <span>CO2排放量：</span>
                <span>${(params.value).toFixed(2)}吨/人</span>
              </div>
            </div>
            `
          }
        },
        grid:{
          left: 50,
          right: 100,
          top: 50,
          bottom: 50
        },
        dataZoom: [
          {
            type: 'slider',
            xAxisIndex: [0],
            filterMode: 'filter'
          },
          {
            type: 'slider',
            yAxisIndex: [0],
            filterMode: 'filter'
          },
          {
            type: 'inside',
            xAxisIndex: [0],  // 控制 X 轴缩放
            filterMode: 'filter'
          },
        ],
        yAxis: {
          type: 'value'
        },
        xAxis: {
          type: 'category',
          data: this.co2AllData.map(item=>(item.Year))
        },
        series
      }
      myChart.setOption(option)
      // console.log(option)
    },
    renderChartNumCo2(){
      let choseData=[]
      this.co2NumData.forEach((item)=>{
        if(item['Country Name']==='China'){
          choseData=item
        }
      })
      // console.log(choseData)
      const dataS=Object.keys(choseData).filter(key => key !== "Country Name")
      // console.log(dataS)
      let series=[]
      dataS.forEach((item=>{
        // console.log(item)
        series.push(
          {
            name:item,
            type:"bar",
            stack:"1",
            data:[choseData[item]],
          },)
      }))
      const myChart = echarts.init(document.getElementById('chartsNumCo2'))
      const option = {
        legend: {
          show:false,
        },
        tooltip:{
          show:true,
          formatter:function(params){
            return `
            <div>
              <div>
                <span>年份：</span>
                <span>${params.seriesName}</span>
              </div>
              <div>
                <span>CO2排放量：</span>
                <span>${(params.value*1).toFixed(2)}吨/人</span>
              </div>
            </div>
            `
          }
        },
        grid:{
          left: 50,
          right: 100,
          top: 50,
          bottom: 50
        },
        xAxis: {
          type: 'value'
        },
        yAxis: {
          type: 'category',
          data: ['china']
        },
        series
      }
      myChart.setOption(option)
      // console.log(option)
    }
  }
}
</script>

<style scoped lang="scss">

</style>
