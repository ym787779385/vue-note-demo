<template>
    <div class="hainan">
        <div id="map1"></div>
    </div>
</template>

<script>
    import echarts from 'echarts'
    import 'echarts-gl'
    import '../../../../node_modules/echarts/map/js/province/hainan.js'
    import hainan from './hainan.json'
    import img from '../../../assets/imgs/hainan-weixing3.jpg'
    const geoCoordMap = {
        东方市: [108.653789, 19.10198],
        屯昌县: [110.102773, 19.362916],
        万宁市: [110.388793, 18.796216],
        临高县: [109.687697, 19.908293],
        昌江黎族自治县: [109.053351, 19.260968]
    }
    export default {
        name: `hainan`,
        data () {
            return {
                mindata: [],
                data: []
            }
        },
        methods: {
            convertData (data) {
                var res = []
                for (var i = 0; i < data.length; i++) {
                    var geoCoord = geoCoordMap[data[i].name]
                    if (geoCoord) {
                        res.push({
                            name: data[i].name,
                            value: geoCoord.concat(data[i].value)
                        })
                    }
                }
                return res
            },
            onBack () {
                this.$emit(`onBack`)
            }
        },
        mounted () {
             for (const key in geoCoordMap) {
                this.data.push({
                    name: key,
                    value: Math.floor(Math.random() * (150 - 50 + 1) + 50)
                })
            }
            const mindata = []
            this.data.forEach(element => {
                mindata.push(element.value)
            })
            echarts.registerMap(`海南`, hainan)
            // 图表配置项
            const option = {
                title: { // 标题
                    top: `5%`,
                    text: ``,
                    subtext: ``,
                    x: `center`,
                    textStyle: {
                        color: `#ccc`
                    }
                },

                tooltip: { // 提示框
                    trigger: `item`,
                    formatter: function (params) {
                        return params.name
                    }
                },
                geo3D: {
                    map: `海南`,
                    left: `10%`,
                    top: `-10%`,
                    label: {
                        show: true,
                        textStyle: {
                            color: `#fff`, // 地图初始化区域字体颜色
                            fontSize: 12, // 字体大小
                            opacity: 1, // 字体透明度
                            backgroundColor: `rgba(0,23,11,0)` // 字体背景色
                        }
                    },
                    //roam: false,
                    //zoom: 4,
                    itemStyle: {
                        opacity: 1,
                        borderWidth: 1
                    },
                    emphasis: {
                        label: {
                            show: true,
                            textStyle: {
                                color: `#ff0`,
                                fontSize: 12, // 字体大小
                                opacity: 1, // 字体透明度
                                backgroundColor: `rgba(0,23,11,0)` // 字体背景色
                            }
                        },
                        itemStyle: {
                            areaColor: `#2B91B7`
                        }
                    },
                    viewControl: {
                        autoRotate: false,
                        minBeta: -180,
                        maxBeta: 180
                    },
                    regions: [],
                    shading: `realistic`,
                    realisticMaterial: {
                        detailTexture: img, // 纹理贴图
                        textureTiling: 1, // 纹理平铺，1是拉伸，数字表示纹理平铺次数
                        roughness: 0, // 材质粗糙度，0完全光滑，1完全粗糙
                        metalness: 0, // 0材质是非金属 ，1金属
                        roughnessAdjust: 0
                    },
                    // 光照阴影
                    light: {
                        main: {
                            color: `#fff`, // 光照颜色
                            intensity: 1, // 光照强度
                            // shadowQuality: 'high', //阴影亮度
                            shadow: true, // 是否显示阴影
                            alpha: 30,
                            beta: 10
                        },
                        ambient: {
                            intensity: 0.1
                        }
                    },
                    // 是否显示地面
                    groundPlane: {
                        show: false
                    }
                },
                // dataRange: {
                //     show: true,
                //     min: 0,
                //     max: 10,
                //     x: `right`,
                //     color: [`#0080FF`, `#D2E9FF`],
                //     text: [`高`, `低`],           // 文本，默认为数值文本
                //     calculable: true
                // },
                series: [
                    {
                        name: `bar3D`,
                        type: `bar3D`,
                        coordinateSystem: `geo3D`,
                        barSize: 1, //柱子粗细
                        shading: `lambert`,
                        opacity: 1,
                        bevelSize: 0.3,
                        minHeight: 0.05,
                        label: {
                            show: false,
                            formatter: `{b}`
                        },
                        data: this.convertData(this.data)
                    }
                    // {
                    //     name: `scatter3D`,
                    //     type: `effectScatter`,
                    //     coordinateSystem: `geo3D`,
                    //     showEffectOn: `render`,
                    //     zlevel: 1,
                    //     rippleEffect: {
                    //         period: 15,
                    //         scale: 4,
                    //         brushType: `fill`
                    //     },
                    //     hoverAnimation: true,
                    //     label: {
                    //         normal: {
                    //             formatter: `{b}`,
                    //             position: `right`,
                    //             offset: [0, 0],
                    //             color: `#1DE9B6`,
                    //             show: true
                    //         }
                    //     },
                    //     itemStyle: {
                    //         normal: {
                    //             color: `#1DE9B6`,
                    //             shadowBlur: 10,
                    //             shadowColor: `#333`
                    //         }
                    //     },
                    //     symbolSize: 12,
                    //     data: [
                    //         { value: [108.653789, 19.10198], itemStyle: { color: `#4ab2e5` } },
                    //         { value: [110.102773, 19.362916], itemStyle: { color: `#4fb6d2` } },
                    //         { value: [110.388793, 18.796216], itemStyle: { color: `#4fb6d2` } },
                    //         { value: [109.687697, 19.908293], itemStyle: { color: `#4fb6d2` } },
                    //         { value: [109.053351, 19.260968], itemStyle: { color: `#4fb6d2` } }
                    //     ]
                    // }
                ]
            }
            const echartMap_ = echarts.init(document.getElementById(`map1`))
            echartMap_.setOption(option)
        }
    }
</script>
<style lang="scss" scoped>
    .hainan {
        width: 100%;
        height: 100%;
        position: relative;
        #map1 {
            width: 100%;
            height: 100%
        }
    }
</style>
