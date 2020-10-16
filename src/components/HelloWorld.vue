<template>
  <div id="menu">
    <h1>{{ msg }}</h1>
    <!-- <div id="charts" style="width: 800px; height: 500px"></div> -->
  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  props: {
    msg: String
  },
  mounted() {
    // this.initEcharts();
  },
  methods: {
    initEcharts() {
      let myChart = this.$echarts.init(
        document.getElementById("charts"),
        "dark"
      );
      // 绘制图表
      myChart.setOption({
        title: { text: "在Vue中使用echarts" },
        tooltip: {},
        // backgroundColor: '#2c343c',
        // visualMap: {
        //   show: false,
        //   min: 80,
        //   max: 600,
        //   inRange: {
        //     colorLightness: [0.1, 0]
        //   }
        // },
        grid: {
          left: 30,
          right: 30,
          height: '80%',
          width: '80%',
          bottom: 80
        },
        xAxis: {
          data: ["衬衫", "羊毛衫", "雪纺衫", "裤子", "高跟鞋", "袜子"]
        },
        yAxis: {},
        dataZoom: [
          {
            type: 'slider',
            start: 10,
            end: 60
          }
        ],
        series: [
          { type: "line", name: "数量", data: [35, 40, 50, 80] },
          {
            type: "bar",
            name: "数量",
            data: [15, 30, 20, 30],
            color: [
              "#ea7e53",
              "#eedd78",
              "#73a373",
              "#73b9bc",
              "#7289ab",
              "#91ca8c",
              "#f49f42"
            ]
          },
          {
            type: "pie",
            radius: "25%",
            center: [200, 140],
            roseType: "angle", // 通过半径表示数据大小
            label: {
              normal: {
                textStyle: { color: `rgba(255, 255, 255, 0.8)`}
              }
            },
            labelLine: {
              normal: {
                lineStyle: {
                  color: "rgba(156, 178, 12, 0.9)"
                }
              }
            },
            itemStyle: {
              // 鼠标的hover高亮样式
              emphasis: {
                shadowBlur: 200,
                shadowColor: "rgba(0, 0, 0, 0.5)"
              },
              normal: {
                // color: "#c23531",
                // 阴影大小
                shadowBlur: 200,
                // 阴影水平方向上的偏移
                shadowOffsetX: 0,
                // 阴影垂直方向上的偏移
                shadowOffsetY: 0,
                // 阴影颜色
                shadowColor: "rgba(0, 0, 0, 0.5)"
              }
            },
            data: [
              { name: "xxx", value: 22 },
              { name: "yy", value: 45 },
              { name: "zz", value: 35 }
            ]
          }
        ]
      });
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="less">
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
