<template>
  <div>
    <div class="echart" id="bb_visual" :style="{width: '100%', height: '800px'}"></div>
    <div class="block">
      <span class="demonstration">选择扫描条带的范围</span>
      <el-slider v-model="slider_value" :format-tooltip="formatTooltip" @input="sliderValueChange"></el-slider>
    </div>
  </div>

</template>

<script>
import * as echarts from 'echarts';
import 'echarts-gl';
export default {
  name: "BB_Visual",
  data() {
    return {
      myChart: {},
      option: {},
      symbolSize: 2.5,
      slider_value: 0,
      start: 0,
      end: 200,
    }
  },
  mounted() {
    this.show_BBTop();
  },
  methods:{
    show_BBTop() {
      let chartDom = document.getElementById('bb_visual');
      this.myChart = echarts.init(chartDom);

      const url = "http://127.0.0.1:5000/api/BBTop";
      this.axios.get(url, {params: {start: this.start, end: this.end}})
          .then((res) => {
            console.log(res.data)
            this.option = {
              grid3D: {
                boxWidth: 200,
                boxDepth: 49,
              },
              xAxis3D: {
                type: 'category',
              },
              yAxis3D: {
                type: 'category',
              },
              zAxis3D: {},
              dataset: {
                dimensions: [
                  'X',
                  'Y',
                  'BBTop',
                  'BBBottom',
                  'BBPeak',
                  'ZeroDeg'
                ],
                source: res.data
              },
              series: [
                {
                  type: 'scatter3D',
                  symbolSize: this.symbolSize,
                  encode: {
                    x: 'X',
                    y: 'Y',
                    z: 'BBTop',
                  },
                  color: '#73c0de',
                },
                {
                  type: 'scatter3D',
                  symbolSize: this.symbolSize,
                  encode: {
                    x: 'X',
                    y: 'Y',
                    z: 'BBBottom',
                  },
                  color: '#5470c6',
                },
                {
                  type: 'scatter3D',
                  symbolSize: this.symbolSize,
                  encode: {
                    x: 'X',
                    y: 'Y',
                    z: 'BBPeak',
                  },
                  color: '#91cc75',
                },
                {
                  type: 'scatter3D',
                  symbolSize: 1,
                  encode: {
                    x: 'X',
                    y: 'Y',
                    z: 'ZeroDeg',
                  },
                  color: '#fc8452',
                },
                // ['#5470c6',
                //   '#91cc75',
                //   '#fac858',
                //   '#ee6666',
                //   '#73c0de',
                //   '#3ba272',
                //   '#fc8452',
                //   '#9a60b4',
                //   '#ea7ccc'],
              ]
            };
            this.myChart.setOption(this.option);
          })
          .catch((error) => console.log(error))
    },
    formatTooltip(val) {
      this.start = Math.round(val*77.35)
      this.end = Math.round(val*77.35+200)
      return Math.round(val*77.35) + ' - ' + Math.round(val*77.35+200);
    },
    sliderValueChange() {
      const url = "http://127.0.0.1:5000/api/BBTop";
      this.axios.get(url, {params: {start: this.start, end: this.end}})
          .then((res) => {
            console.log(res.data)
            this.option.dataset.source = res.data;
            this.myChart.setOption(this.option);
          })
          .catch((error) => console.log(error))
    },
  }
}
</script>

<style scoped>
.block {
  width: 60%;
  margin: 0 auto;
}
</style>