<template>
  <div ref="rankingEchart" class="rankingEchart-wrapper">
  </div>
</template>
<script>
  import { extend, $, resizeMixin } from 'assets/js/common'
  import deepcopy from 'deepcopy'

  require('echarts/lib/component/tooltip')
  require('echarts/lib/component/legend')
  export default {
    name: 'rankingEchart',
    mixins: [resizeMixin],
    //props: ['echartsData'],
    props: {
      data: {
        type: Array,
        default: () => []
      },

    },
    mounted() {
      this.myChart = this.$echarts.init(this.$refs.rankingEchart)
      this.initChart()
    },
    methods: {
      initChart() {
        let echartOpt = {
          fz: '16',
          color: '#fff'
        }
        //获取data里的最大最小值
        Array.prototype.max = function() {
          return Math.max.apply({}, this)
        }
        Array.prototype.min = function() {
          return Math.min.apply({}, this)
        }

        let resData = this.data.map((item) => item.value).sort((a, b) => a - b)

        let maxData = resData.max()

        let xFuzhu = resData.map((item, index) => {
          return maxData
        })


        let option = {
          grid: {
            left: 150,
            right: 80,
            top: 0,
            bottom: 0
          },
          tooltip: {
            formatter: '{b}:{c}',
            backgroundColor: '#099d4f',
          },
          yAxis: [{
            interval: 0,
            axisLabel: {
              margin: 10,
              textStyle: {
                fontSize: echartOpt.fz,
                color: '#fff'
                // color: function(value, index) {
                //   return index >= 7 ? '#eeeff1' : '#63676e';
                // }

              }
            },
            splitLine: {
              show: false
            },
            axisTick: {
              show: false
            },
            axisLine: {
              show: false
            },
            data: deepcopy(this.data).sort((a, b) => a.value - b.value).map((item) => item.name)
          }, {
            // 辅助y 轴
            show: true,
            interval: 0,
            axisLabel: {
              textStyle: {
                fontSize: echartOpt.fz,
                color: function(value, index) {
                  return index >= 7 ? 'red' : '#fff';
                }
              }
            },
            splitLine: {
              show: false
            },
            axisTick: {
              show: false
            },
            axisLine: {
              show: false,
              lineStyle: {
                color: echartOpt.color,
                fontSize: echartOpt.fz
              }
            },
            offset: '50',
            // data: this.data.map((item) => item.name),
            position: 'left',
          }],
          xAxis: {
            max: maxData * 1.5,
            max: 'auto',
            show: false,
            axisLine: {
              show: false
            }
          },
          color: ['#162f66'],
          series: [{
            // 辅助系列
            type: 'bar',
            silent: true,
            barGap: '-100%',
            itemStyle: {
              normal: {
                color: '#1e3045',
              }
            },
            barWidth: 10,
            data: xFuzhu
          }, {
            type: 'bar',
            // data: this.data.data,
            data: resData,
            barWidth: 10,
            label: {
              normal: {
                position: 'right',
                show: true,
                textStyle: {
                  fontSize: echartOpt.fz,
                  color: "#3aa1e8"
                },
                formatter: (value) => {value.data}
              }
            },
            itemStyle: {
              normal: {
                //barBorderWidth:10,
                //barBorderRadius: 5,
                color: '#40b0fc'
              },
              emphasis: {
                color: '#099d4f',
              }
            }
          }]
        }

        this.myChart.setOption(option);
      },
      _windowResizeHandler() {
        this.myChart.resize()
      },
      _destroyEchart() {
        this.myChart.dispose()
      }
    },
    watch: {
      data: {
        handler: function(newVal, oldVal) {
          this.initChart()
        },
        deep: true //增加deep 观察对象的子对象变化
      }
    },
  }

</script lang="scss" scoped>
  @import "./../../../assets/css/_variable.scss";
  @import "./../../../assets/css/_mixin.scss";

  .rankingEchart-wrapper {
    width: 100%;
    height: 100%;
  }

</style>
