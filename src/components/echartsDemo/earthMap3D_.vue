/* eslint-disable */
<template>
  <div class="container">
    <div id="earth_map_3D"></div>
  </div>
</template>
<script>
import echarts from 'echarts'
import 'echarts-gl'
import '../../../../node_modules/echarts/map/js/world.js'
import wordLight from '@/assets/imgs/word_light.png'
import word from '@/assets/imgs/word.jpg'
import * as d3 from 'd3'
export default {
  name: `earthMap3D_`,
  mounted () {
    this.getConfig()
  },
  beforeDestroy () {
    echarts.init(document.getElementById(`earth_map_3D`)).dispose()
  },
  methods: {
    getConfig () {
      const config = { // 扫描线条配置
        lineWidth: 0.5, // 扫描线条宽度
        color: `#00FFC2`, // 线条颜色
        levels: 1,
        intensity: 3, // 强度
        threshold: 0.01
      }
      const canvas = document.createElement(`canvas`)
      canvas.width = 4096
      canvas.height = 2048
      const context = canvas.getContext(`2d`)
      context.lineWidth = config.lineWidth
      context.strokeStyle = config.color
      context.fillStyle = config.color
      context.shadowColor = config.color
      this.image(word).then(image => {
        const m = image.height
        const n = image.width
        const values = new Array(n * m)
        const contours = d3.contours().size([n, m]).smooth(true)
        const projection = d3.geoIdentity().scale(canvas.width / n)
        const path = d3.geoPath(projection, context)
        for (var j = 0, k = 0; j < m; ++j) {
          for (var i = 0; i < n; ++i, ++k) {
            values[k] = image.data[(k << 2)] / 255
          }
        }
        const opt = {
          image: canvas
        }
        let results = []
        function update (threshold, levels) {
          context.clearRect(0, 0, canvas.width, canvas.height)
          var thresholds = []
          for (var i = 0; i < levels; i++) {
              thresholds.push((threshold + 1 / levels * i) % 1)
          }
          results = contours.thresholds(thresholds)(values)
          redraw()
        }
        function redraw () {
          results.forEach(function (d, idx) {
              context.beginPath()
              path(d)
              context.globalAlpha = 1
              context.stroke()
              if (idx > config.levels / 5 * 3) {
                  context.globalAlpha = 0.01
                  context.fill()
              }
          })
          opt.onupdate()
        }
        d3.timer(t => {
          var threshold = (t % 10000) / 10000
          update(threshold, 1)
        })
        this.initCharts(opt, config)
        update(config.threshold, config.levels)
      })
    },
    image (url) {
      return new Promise(resolve => {
        const image = new Image()
        image.src = url
        image.onload = function () {
          const canvas = document.createElement(`canvas`)
          canvas.width = image.width / 8
          canvas.height = image.height / 8
          const context = canvas.getContext(`2d`)
          context.drawImage(image, 0, 0, canvas.width, canvas.height)
          resolve(context.getImageData(0, 0, canvas.width, canvas.height))
        }
      })
    },
    initCharts (opt, config) {
      var contourChart = echarts.init(document.createElement(`canvas`), null, {
        width: 4096,
        height: 2048
      })
      var img = new echarts.graphic.Image({
        style: {
          image: opt.image,
          x: -1,
          y: -1,
          width: opt.image.width + 2,
          height: opt.image.height + 2
        }
      })
      contourChart.getZr().add(img)
      opt.onupdate = function () {
        img.dirty()
      }
      const myChart = echarts.init(document.getElementById(`earth_map_3D`))
      window.onresize = function () {
        myChart.resize()
      }
      myChart.setOption(
        {
          backgroundColor: `rgba(0,0,0,0)`,
          globe: {
            baseTexture: wordLight,
            displacementScale: 0.05,
            displacementQuality: `high`,
            shading: `realistic`,
            realisticMaterial: {
              roughness: 0.2,
              metalness: 0
            },
            light: {
              ambient: {
                intensity: 1
              },
              main: { // 主光源
                intensity: 0,
                shadow: false
              }
            },
            postEffect: {
              enable: true,
              bloom: {
                enable: true
              },
              depthOfField: {
                enable: false
              }
            },
            viewControl: {
              center: [0, 0, 0],
              alpha: 30,
              beta: 160,
              distance: 160,
              autoRotate: true,
              autoRotateAfterStill: 10,
              autoRotateSpeed: 20
            },
            layers: [{
              type: `blend`,
              blendTo: `emission`,
              texture: contourChart,
              intensity: config.intensity
            }]
          },
          series: [{ // 点
            type: `scatter3D`,
            blendMode: `lighter`,
            coordinateSystem: `globe`,
            showEffectOn: `render`,
            zlevel: 2,
            effectType: `ripple`,
            symbolSize: 15,
            rippleEffect: {
              period: 4,
              scale: 4,
              brushType: `fill`
            },
            emphasis: {
              itemStyle: {
                color: `red`,
                opacity: 1
              }
            },
            hoverAnimation: true,
            itemStyle: {
              normal: {
                color: `rgba(235, 232, 6, 1)`
              }
            },
            data: [
              [51.511744, 25.292405],
              [30.5234, 50.450099],
              [24.940524, 60.170675],
              [30.30604, 59.933177]
            ]
          }
          ]
        }
      )
      myChart.on(`click`, (params) => {
        console.log(`dianji shijian`)
        this.$emit(`onEarthCallback`, params)
      })
    }
  }
}
</script>
<style lang="scss" scoped>
.container{
  height: 100%;
  background: url("~@/assets/imgs/map3D.jpg") no-repeat;
  background-size: 100% 100%;
  #earth_map_3D{
    height: 85%;
    width: 100%;
  }
}
</style>
