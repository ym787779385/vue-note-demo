/**
* @date: 2020/10/16 9:32
* @update: 2020/10/16 9:32
* 一维作为三维地图的贴图
*/

<template>
    <div class="hainan">
        <div id="chart"></div>
        <img src="~@/assets/imgs/mapbg2.png" alt="">
        <div :v-html="bg"></div>
    </div>
</template>

<script>
    import echarts from 'echarts'
    import 'echarts-gl'
   // import '../../../../node_modules/echarts/map/js/province/hainan.js'
    import hainan from './hainan3.json'
    const geoCoordMap = {
        东方市: [108.653789, 19.10198],
        屯昌县: [110.102773, 19.362916],
        万宁市: [110.388793, 18.796216],
        临高县: [109.687697, 19.908293],
        昌江黎族自治县: [1109.053351, 19.260968],
        海口市: [110.33119, 20.031971],
        三亚市: [109.508268, 18.247872],
        儋州市: [109.576782, 19.517486],
        五指山市: [109.516662, 18.776921]
    }
    export default {
        name: `hainan`,
        data () {
            return {
                bg: '',
                data: [],
                imageDom: null,
                chartOption: {
                    geo: {
                        // backgroundColor: {
                        //     color: {
                        //         image: this.imageDom, // 支持为 HTMLImageElement, HTMLCanvasElement，不支持路径字符串
                        //         repeat: 'repeat' // 是否平铺，可以是 'repeat-x', 'repeat-y', 'no-repeat'
                        //     }
                        // },
                        show: true,
                        map: `海南`,
                        left: '0',
                        top: `0%`,
                        right: '8%',
                        bottom: '0',
                        itemStyle: {
                            normal: {
                                areaColor: `rgba(115, 219, 249, 0)`,
                                borderwidth: 3,
                                borderColor: `#37C1FD`,
                                shadowBlur: 20,
                                shadowOffsetY: 4,
                                shadowOffsetX: 4,
                                shadowColor: `#ddd`
                            }
                        }
                    },
                    series: [
                         {
                            type: `effectScatter`,
                            coordinateSystem: `geo`,
                            rippleEffect: { //涟漪特效
                                period: 4, //动画时间，值越小速度越快
                                brushType: `stroke`, //波纹绘制方式 stroke, fill
                                scale: 8 //波纹圆环最大限制，值越大波纹越大
                            },
                            itemStyle: {
                                normal: {
                                    color: `orange`,
                                    shadowBlur: 2
                                }
                            },
                            symbolSize: 8,
                            data: [
                                { name: `三亚市`, value: [109.508268, 18.247872] },
                                { name: `五指山市`, value: [109.516662, 18.776921] }
                                //[109.508268, 18.247872, `三亚市`],
                                //[109.516662, 18.776921, `五指山市`]
                            ]
                        }
                    ]
                }
            }
        },
        methods: {
            initChart () {
                echarts.registerMap(`海南`, hainan)
                const canvas = document.createElement(`canvas`)
                this.bg = echarts.init(canvas, null, {
                    width: 1024,
                    height: 1024
                })
                this.bg.setOption(this.chartOption)
                const options_ = {
                     geo3D: {
                        map: `海南`,
                        viewControl: {
                            autoRotate: false,
                            distance: 180
                        },
                        shading: `color`,
                        boundingCoords: [
                            [-180, 90],
                            [180, -90]
                        ],
                        colorMaterial: {
                            detailTexture: this.bg, // 纹理贴图
                            textureTiling: 1 // 纹理平铺，1是拉伸，数字表示纹理平铺次数
                        },
                        // 是否显示地面
                        groundPlane: {
                            show: false
                        }
                    }
                }
                const echartMap_ = echarts.init(document.getElementById(`chart`))
                echartMap_.setOption(options_)
                }
        },
        mounted () {
            this.initChart()
        }
    }
</script>

<style lang="scss" scoped>
    .hainan {
        height: 100vh;
        width: 100vw;
        //background-color: #34ffff;
        position: relative;
        display: flex;
        background-color: black;
        #chart {
            position: relative;
            width: 100vh;
            height: 100vh;
            z-index:1;
            //background: url(~@/assets/imgs/mapbg2.png) no-repeat center;
            background-size: 100% 100%;
            border: 1px dotted red;
        }
        img{
            width: 20%;
            height: auto;
        }
    }
</style>
