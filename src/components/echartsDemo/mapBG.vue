// 二维地图设置背景
<template>
  <div class="smuggling">
    <div class="chartsTitleNoMore">{{title}}</div>
    <div class="map" :id="id"></div>
    <div class="lenge">
      <div v-for="item in lengList" :key="item.name + `_lenge`">
        <img :src="item.imgUrl" alt=""><span>{{item.name}}</span>
      </div>
    </div>
  </div>
</template>
<script>
import echarts from 'echarts'
import hainanGeoJson from './hainan_geojson.json'
import mapBj from './mapBJ.png'
export default {
  name: `smugglingMap`,
  props: {
    title: {
      type: String,
      default: ``,
      require: false
    },
    id: {
      type: String,
      require: true,
      default: `map`
    },
    // 地图类型支持 world/china
    type: {
      type: String,
      require: false,
      default: `海南`
    },
    size: {
      type: Number,
      require: false,
      default: 46
    },
    imgList: {
      type: Array,
      require: false,
      default: () => {
        return [
          require(`@/assets/images/hainaMap/people.png`),
          require(`@/assets/images/hainaMap/che.png`),
          require(`@/assets/images/hainaMap/chuan.png`)
        ]
      }
    },
    lengList: {
      type: Array,
      require: false,
      default: () => {
        return [
          {
            name: `有走私前科人员`,
            imgUrl: require(`@/assets/images/hainaMap/people_1.png`)
          },
          {
            name: `有走私前科车辆`,
            imgUrl: require(`@/assets/images/hainaMap/che_1.png`)
          },
          {
            name: `有走私前科船舶`,
            imgUrl: require(`@/assets/images/hainaMap/chuan_1.png`)
          }
        ]
      }
    },
    flowData: {
      type: Array,
      require: true,
      default: () => {
        return [
          [// 人员的数据
            {
              name: `人员`,
              number: `张三`,
              sort: 1,
              color: `redBorder`,
              value: [109.516662, 18.776921]
            }
          ],
          [// 车辆的数据
            {
              name: `车辆`,
              number: `A120510`,
              sort: 3,
              color: `orangeBorder`,
              value: [110.102773, 19.362916]
            },
            {
              name: `车辆`,
              sort: 4,
              color: `orangeBorder`,
              number: `A120510`,
              value: [109.053351, 19.260968]
            }
          ],
          [// 船的数据
            {
              name: `船`,
              sort: 5,
              color: `orangeBorder`,
              number: `A120510`,
              value: [108.653789, 19.10198]
            }
          ]
          // [// 点的数据
          //   {
          //     name: `海口市`,
          //     value: [110.33119, 20.031971]
          //   }
          // ]
        ]
      }
    }
  },
  data () {
    return {
      mapChart: ``,
      imageBase: [],
      bj: ``,
      options: {
          tooltip: {
            triggerOn: `onmousemove`,
            trigger: `item`,
            // position: `top`,
            position: function (point, params, dom, rect, size) {
              // 鼠标坐标和提示框位置的参考坐标系
              let x = 0 // x坐标位置
              let y = 0// y坐标位置
              const pointX = point[0]
              const pointY = point[1]
              const boxHeight = size.contentSize[1]
              if (size.contentSize[0] > pointX) {
                x = 5
              } else {
                x = pointX - 10
              }
              if (boxHeight > pointY) {
                y = 5
              } else {
                y = pointY - boxHeight - 10
              }
              return [x, y]
            },
            backgroundColor: `rgba(0,0,0,0)`,
            formatter: function (params) {
              const str = `<div class="labelTool ${params.data.color}">
                <div>${params.name}信息</div>
                <div><span>${params.data.name === `人员` ? `姓名: ` : `车牌: `}</span>${params.data.number}</div>
                <div><span>风险排名：</span>${params.data.sort}名</div>
              </div>`
              return str
            }
          },
          geo: [
            {
                map: this.type,
                aspectScale: 1,
                roam: false, //是否允许缩放
                layoutSize: `95%`,
                layoutCenter: [`50%`, `50%`],
                emphasis: {
                  label: {
                    color: `#fff`
                  }
                },
                itemStyle: {
                  areaColor: `transparent`,
                  borderColor: `#16ecfc`,
                  borderWidth: 1,
                  shadowBlur: 6,
                  shadowOffsetY: 0,
                  shadowOffsetX: 0,
                  shadowColor: `#16ecfc`,
                  emphasis: {
                    areaColor: `rgba(63, 218, 255, 0.1)`,
                    label: {
                      color: `#fff`
                    }
                  }
                },
                z: 4
            }, {
                map: this.type,
                aspectScale: 1,
                roam: false, //是否允许缩放
                //zoom: 1.1, //默认显示级别
                layoutSize: `95%`,
                layoutCenter: [`50%`, `50%`],
                itemStyle: {
                    color: {
                      image: this.bj, // 支持为 HTMLImageElement, HTMLCanvasElement，不支持路径字符串
                      repeat: `repeat` // 是否平铺，可以是 'repeat-x', 'repeat-y', 'no-repeat'
                    },
                    borderColor: `#37C1FD`,
                    borderWidth: 2
                },
                z: 3,
                silent: true
            },
            {
                map: this.type,
                aspectScale: 1,
                roam: false, //是否允许缩放
                //zoom: 1.1, //默认显示级别
                layoutSize: `96%`,
                layoutCenter: [`50.1%`, `52.2%`],
                itemStyle: {
                    areaColor: `#0C366F`,
                    borderColor: `#0C366F`
                },
                z: 2,
                silent: true
            },
            {
                map: this.type,
                aspectScale: 1,
                roam: false, //是否允许缩放
                //zoom: 1.1, //默认显示级别
                layoutSize: `96%`,
                layoutCenter: [`50.1%`, `52.2%`],
                itemStyle: {
                    areaColor: `#0C366F`,
                    borderColor: `#16ecfc`,
                    borderWidth: 4,
                    shadowBlur: 60,
                    shadowOffsetY: 0,
                    shadowOffsetX: 0,
                    shadowColor: `#239fe7`
                },
                z: 1,
                silent: true
            }
          ],
          series: [
            {
              name: `人员`,
              type: `scatter`,
              coordinateSystem: `geo`,
              data: [],
              zlevel: 5,
              symbol: ``,
              symbolSize: 34,
              itemStyle: {
                  normal: {
                      //color: 'green',
                      borderWidth: 0
                  }
              }
            },
            {
              name: `车辆`,
              type: `scatter`,
              coordinateSystem: `geo`,
              data: [],
              zlevel: 5,
              symbol: ``,
              symbolSize: 34,
              itemStyle: {
                  normal: {
                    borderWidth: 0
                  }
              }
            },
            {
              name: `船舶`,
              type: `scatter`,
              coordinateSystem: `geo`,
              data: [],
              zlevel: 5,
              symbol: ``,
              symbolSize: 34,
              itemStyle: {
                  normal: {
                    borderWidth: 0
                  }
              }
            }
            // {
            //   type: `effectScatter`,
            //   coordinateSystem: `geo`,
            //   zlevel: 4,
            //   symbolSize: 6,
            //   rippleEffect: {
            //     scale: 9,
            //     brushType: `stroke`
            //   },
            //   itemStyle: {
            //     normal: {
            //       color: `yellow`
            //     }
            //   },
            //   data: []
            // },
          ]
        }
    }
  },
  watch: {
    flowData: {
      handler (newValue, oldValue) {
        this.setOptionsValue()
      },
      deep: true
    },
    type: {
      handler (newValue, oldValue) {
        console.log(`gaibian`)
        this.setOptionsValue()
      }
    }
  },
  methods: {
    setOptionsValue () {
      this.options.geo[1].itemStyle.color.image = this.bj
      this.options.series[0].symbol = this.imageBase[0]
      this.options.series[1].symbol = this.imageBase[1]
      this.options.series[2].symbol = this.imageBase[2]
      this.options.series.forEach((el, index) => {
        this.options.series[index].data = this.flowData[index]
        this.options.series[index].symbolSize = this.size
        console.log(this.flowData[index])
      })
      this.options.series[0].map = this.type
      this.options.geo.forEach(e => {
        e.map = this.type
      })
      this.mapChart.setOption(this.options, true)
    },
    image (url) {
      return new Promise(resolve => {
          const image = new Image()
          image.src = url
          image.onload = function () {
            resolve(image)
          }
      })
    },
    getImage (img) {
      return new Promise(resolve => {
        const image = new Image()
        image.src = img
        image.onload = () => {
          const canvas = document.createElement(`canvas`)
          canvas.width = image.width
          canvas.height = image.height
          const ctx = canvas.getContext(`2d`)
          const x = canvas.width / 2
          const y = canvas.height / 2
          // 将绘图原点移到画布中心
          ctx.translate(x, y)
          // 旋转角度
          ctx.rotate((Math.PI / 180) * (Math.floor(Math.random() * 6) * 10))
          // 将画布原点移动
          ctx.translate(-x, -y)
          // 绘制图片
          ctx.drawImage(image, 0, 0, image.width, image.height)
          const ext = image.src.substring(image.src.lastIndexOf(`.`) + 1).toLowerCase()
          const dataURL = canvas.toDataURL(`image/` + ext)
          console.log(dataURL)
          resolve(`image://` + dataURL)
        }
      })
    }
  },
  async mounted () {
    this.bj = await this.image(mapBj)
    const imgbase_ = await this.getImage(this.imgList[0])
    const imgbase2_ = await this.getImage(this.imgList[1])
    const imgbase3_ = await this.getImage(this.imgList[2])
    this.imageBase.push(...[imgbase_, imgbase2_, imgbase3_])
    this.$nextTick(() => {
      echarts.registerMap(`海南`, hainanGeoJson)
      echarts.init(document.getElementById(this.id)).dispose()
      this.mapChart = echarts.init(document.getElementById(this.id))
      this.setOptionsValue()
      window.addEventListener(`resize`, () => {
          this.mapChart.resize() // 切换浏览器大小时必须添加该方法，否则图形显示会错位
      })
    })
  }
}
</script>
<style lang="scss" scoped>
  .smuggling {
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: column;
    background: url("~@/assets/images/map3D.jpg") no-repeat;
    background-size: cover;
    position: relative;
    .chartsTitleNoMore{
      color: #8EC5FC;
      font-size: 12px;
      font-weight: bolder;
      padding: 10px 20px 10px 4px;
      &::before{
          content: '';
          display: inline-block;
          width: 4px;
          background-color: #57CDF6;
          margin-right: 4px;
          margin-top: -3px;
          height: 12px;
          vertical-align: middle;
      }
    }
    .map {
      flex: 1;
    }
    /deep/.labelTool{
      padding-bottom: 10px;
      >div{
        padding: 5px 10px;
        margin: 1px;
        background-color:rgba($color: #000000, $alpha: 0.3);
        >span{
          color: rgb(231, 230, 230);
          font-weight: bold;
        }
        &:nth-child(1) {
          font-weight: bold;
          font-size: 14px;
          border-top-right-radius: 6px;
          border-top-left-radius: 6px;
        }
      }
    }
    /deep/.redBorder{
      background: url("~@/assets/images/hainaMap/border_red.png") no-repeat;
       background-size: 100% 100%;
       >div{
        padding: 5px 10px;
        &:nth-child(1) {
          background-color: rgba(255, 68, 11, 0.8);
        }
      }
    }
    /deep/.greenBorder{
      background: url("~@/assets/images/hainaMap/border_green.png") no-repeat;
       background-size: 100% 100%;
       >div{
        padding: 5px 10px;
        &:nth-child(1) {
          background-color: rgba(11, 255, 196, 0.8);
        }
      }
    }
    /deep/.orangeBorder{
      background: url("~@/assets/images/hainaMap/border_orange.png") no-repeat;
      background-size: 100% 100%;
       >div{
        padding: 5px 10px;
        &:nth-child(1) {
          background-color: rgba(255, 175, 37, 0.9);
        }
      }
    }
    .lenge{
      width: 150px;
      height: auto;
      position: absolute;
      background: url("~@/assets/images/hainaMap/lenge_border.png") no-repeat;
      background-size: 100% 100%;
      right: 40px;
      bottom: 40px;
      padding: 20px 10px;
      >div{
        line-height: 30px;
        display: flex;
        align-items: center;
        justify-content: left;
      }
      img{
        width: 16px;
        height: 16px;
        margin-left: 10px;
      }
      span{
        font-size: 12px;
        padding-left: 8px;
      }
    }
  }
</style>
