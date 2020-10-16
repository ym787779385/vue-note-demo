<template>
  <div class="container">
    <div id="earth_map_3D" @click="onClicked"></div>
  </div>
</template>
<script>
import echarts from 'echarts'
import 'echarts-gl'
import '../../../../node_modules/echarts/map/js/world.js'
import { geoCoord } from './wordMap.vue'
import wordLight from './imgs/word_light.png'
export default {
  name: `earthMap3D`,
  data () {
    return {
      dser: []
    }
  },
  mounted () {
    const data = this.getLinkData()
    // 地图背景
    const myChart1 = echarts.init(document.getElementById(`earth_map_3D`))
    window.onresize = function () {
      myChart1.resize()
    }
   // 地图
    const option = {
      backgroundColor: `rgba(0,0,0,0)`, //canvas的背景颜色
      globe: {
        baseTexture: wordLight,
        top: `middle`,
        left: `center`,
        displacementQuality: `high`,
        realisticMaterial: {
          roughness: 0.2,
          metalness: 0
        },
        displacementScale: 0.05,
        environment: `none`,
        shading: `color`,
        viewControl: {
          autoRotateSpeed: 20,
          distance: 200,
          autoRotate: true
        },
        light: {
          ambient: {
            intensity: 1
          },
          main: { // 主光源
            intensity: 0,
            shadow: false
          }
        }
      },
      series: [{ // 点
        type: `lines3D`,
        effect: {
          show: true,
          period: 2,
          trailWidth: 1,
          trailLength: 0.5,
          trailOpacity: 1,
          trailColor: `#0087f4`
        },
        blendMode: `lighter`,
        lineStyle: {
          color: function (params) {
            return `${params.data.toName === `某海关` ? `rgba(111, 218, 255, 0.1)` : `rgba(255, 255, 255, 0.1)`}`
          },
          width: 1,
          opacity: 0.6
        },
        data: data.link
      },
      { // 点
        type: `scatter3D`,
        blendMode: `lighter`,
        coordinateSystem: `globe`,
        showEffectOn: `render`,
        zlevel: 2,
        effectType: `ripple`,
        symbolSize: 5,
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
        data: data.point
      }]
    }
    myChart1.setOption(option, true)
  },
  methods: {
    getLinkData () {
      const linkData = []
      const linkInData = []
      const pointData = []
      for (const key in geoCoord) {
        if (linkData.length < 10) {
          linkData.push({
            coords: [[116.2, 39.55], geoCoord[key]]
          })
        } else if (linkInData.length < 10) {
          linkInData.push({
            toName: `某海关`,
            coords: [geoCoord[key], [116.2, 39.55]]
          })
        }
        if (pointData.length < 20) {
          pointData.push(geoCoord[key])
        }
      }
      return {
        link: [...linkData, ...linkInData],
        point: pointData
      }
    },
    onClicked () {
      this.$emit(`onEarthCallback`, `china`)
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
