<template>
  <div class="hello" style="padding: 10px;">
    <!-- <div>
      <img src="../assets/earth.png" style="width: 120px;width: 120px;position: absolute;left:100px;top: 20px;z-index:10;">
    </div> -->
    <div id="chartstemp" style="height: 500px;width: 100%;"></div>
    <div style="padding: 20px;display: flex;flex-direction: row;">
      <div id="chartsAllCo2" style="height: 500px;width: 50%;"></div>
      <!-- <el-empty v-if="co2ChosedCountry===''" style="height: 500px;width: 50%;" description="请在左侧柱状图内选择一个国家或地区以查看数据"></el-empty> -->
      <div id="chartsNumCo2" style="height: 500px;width: 50%;"></div>
    </div>
    
  </div>
</template>

<script>
import * as echarts from 'echarts'
require('../assets/dark')

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
      co2ChosedCountry:'China'
    }
  },
  mounted(){
    // console.log(temperatureData,co2AllData)
    this.co2AllData = co2AllData
    this.co2NumData = co2NumData
    this.tempData = temperatureData.slice(1)

    this.renderChartAllCo2()
    this.renderTempData()
    if(this.co2ChosedCountry!==''){
      this.renderChartNumCo2()
    }
  },
  methods:{
    renderChartAllCo2(){
      var that = this
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
      // const myChart = echarts.init(document.getElementById('chartsAllCo2'),'dark')
      const option = {
        legend: {
          show:true,
          type:'scroll',
        },
        tooltip:{
          show:true,
          triggerOn: "click",
          formatter:function(params){
            that.co2ChosedCountry=params.seriesName
            that.renderChartNumCo2()
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
          right: 50,
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
            type: 'inside',
            xAxisIndex: [0],
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
      window.addEventListener('resize', function () {
        myChart.resize()
      })
    },
    renderChartNumCo2(){
      let choseData=[]
      this.co2NumData.forEach((item)=>{
        if(item['Country Name']===this.co2ChosedCountry){
          choseData=item
        }
      })
      // console.log(choseData)
      const dataX=Object.keys(choseData).filter(key => key !== "Country Name")
      // console.log(dataX)
      let series=[]
      dataX.forEach((item=>{
        // console.log(item)
        series.push(
          {
            name:item,
            type:"bar",
            stack:"1",
            data:[choseData[item]],
          },)
      }))
      let dataS=[]
      Object.values(choseData).forEach(value => {
        dataS.push(value)
      })
      // console.log(choseData)
      const myChart = echarts.init(document.getElementById('chartsNumCo2'))
      const option = {
        legend: {
          show:false,
        },
        tooltip:{
          show:true,
          trigger: "axis",
          formatter:function(params){
            return `
            <div>
              <div>
                <span>国家或地区：</span>
                <span>${params[0].seriesName}</span>
              </div>
              <div>
                <span>CO2排放量：</span>
                <span>${(params[0].value*1).toFixed(2)}吨/人</span>
              </div>
            </div>
            `
          }
        },
        title:{
          text:`${this.co2ChosedCountry}人均碳排放量`
        },
        grid:{
          left: 20,
          right: 10,
          top: 40,
          bottom: 20
        },
        yAxis: {
          type: 'value'
        },
        xAxis: {
          type: 'category',
          data: dataX
        },
        series: {
          name: this.co2ChosedCountry,
          type: 'line',
          data: dataS,
          itemStyle: {
            color: '#c23531'
          },
          lineStyle: {
            color: '#c23531'
          },
          areaStyle: {
            color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
              { offset: 1, color: 'rgba(194, 53, 49, 0)' }, // 透明
              { offset: 0, color: '#c23531' }  // 深红色
            ])
          }
        }
      }
      myChart.setOption(option)
      window.addEventListener('resize', function () {
        myChart.resize()
      })
    },
    renderTempData(){
      const myChart = echarts.init(document.getElementById('chartstemp'))
      const option = {
        tooltip: {
          trigger: 'axis'
        },
        xAxis: {
          type: 'category',
          data: this.tempData.map(item=>item.year)
        },
        yAxis: {
          type: 'value',
          axisLabel: {
            formatter: '{value} °C'
          }
        },
        grid:{
          left: 50,
          right: 50,
          top: 50,
          bottom: 80
        },
        dataZoom: [
          {
            type: 'slider',
            xAxisIndex: [0],
            filterMode: 'filter'
          },
          {
            type: 'inside',
            xAxisIndex: [0],
            filterMode: 'filter'
          },
        ],
        series: [
          {
            name: '温度变化',
            type: 'line',
            data: this.tempData.map(item=>item.annualMeanTemperature*1),
            areaStyle: {
              color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
                { offset: 0, color: 'red' },   // 温度为正
                { offset: 1, color: 'blue' }  // 温度为负
              ])
            }
          }
        ]
      }
      myChart.setOption(option)
      myChart.resize()
      window.addEventListener('resize', function () {
        myChart.resize()
      })
    }
  }
}
</script>

<style scoped lang="scss">

</style>
