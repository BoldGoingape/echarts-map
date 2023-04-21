<template>
  <div>
    <div class="title">Echarts天津地图二级钻取</div>
    <div class="box">
      <button class="backBtn" @click="back()">返回上级</button>
      <div id="mapEchart" class="chart"></div>
    </div>
    <div class="myBlog">
      <a
        href="https://github.com/BoldGoingape"
        target="_blank"
        title="BoldGoingape"
      >
        <img
          src="https://avatars.githubusercontent.com/u/99953603?s=40&v=4"
          alt="BoldGoingape"
        />
        BoldGoingape
      </a>
    </div>
    <div class="bottom">
      <a
        href="https://github.com/BoldGoingape/echarts-map"
        target="_blank"
        title="点我查看源码"
      >
        <svg
          height="26"
          viewBox="0 0 16 16"
          version="1.1"
          width="26"
          aria-hidden="true"
        >
          <path
            fill-rule="evenodd"
            d="M8 0C3.58 0 0 3.58 0 8c0 3.54 2.29 6.53 5.47 
                    7.59.4.07.55-.17.55-.38 0-.19-.01-.82-.01-1.49-2.01.37-2.53-.49-2.69-.94-.09-.23-.48-.94-.82-1.13-.28-.15-.68-.52-.01-.53.63-.01 1.08.58 1.23.82.72 
                    1.21 1.87.87 2.33.66.07-.52.28-.87.51-1.07-1.78-.2-3.64-.89-3.64-3.95 0-.87.31-1.59.82-2.15-.08-.2-.36-1.02.08-2.12 0 0 .67-.21 2.2.82.64-.18 
                    1.32-.27 2-.27.68 0 1.36.09 2 .27 1.53-1.04 2.2-.82 2.2-.82.44 1.1.16 1.92.08 2.12.51.56.82 1.27.82 2.15 0 3.07-1.87 3.75-3.65 3.95.29.25.54.73.54 
                    1.48 0 1.07-.01 1.93-.01 2.2 0 .21.15.46.55.38A8.013 8.013 0 0 0 16 8c0-4.42-3.58-8-8-8z"
          ></path>
        </svg>
      </a>
    </div>
  </div>
</template>

<script>
import cityMap from "@/js/china-main-city-map.js";
import tianjinmap from "@/js/tianjinmap.js";
import "echarts-gl"; //3D地图插件
import * as echarts from "echarts";
import geoJson from "@/js/bf.json";
import axios from "axios";
import Vue from "vue";
export default {
  name: "chinaMap",
  props: {
    msg: String
  },
  data() {
    return {
      mapflag: true,
      titleText: "天津市",
      // geo3d + map3d公用图表配置
      setting: {
        top: "0%",
        right: "0%",
        bottom: "-20%",
        distance: 90,
        alpha: 15,
        beta: 5,
        regionHeight: 5,
        distance: 150,
        Sensitivity: 5
      },
      option: {
        geo: [
          {
            map: "JS",
            zlevel: 2,
            zoom: 0.88,
            type: "geo3D",
            roam: false,
            // shading: `color`,
            // layoutSize: "80%",
            layoutCenter: ["50%", "50%"],
            label: {
              show: true,
              color: "#eee",
              fontSize: 18
            },
            itemStyle: {
              color: "rgba(128, 128, 128, 0)",
              // color: {
              //   // image: require("@/assets/img/newSy/zl/bj.png"),
              //   // image: require("@/assets/img/newSy/zl/bj1-min.gif"),
              //   // image: this.$store.state.iamgesBuffer[0].src,
              //   repeat: "no-repeat" //可选值repeat、no-repeat、repeat-x、repeat-y
              // }, // 背景

              borderWidth: "1", // 边框宽度
              borderColor: "#adebff" // 边框颜色
            }
          },
          //第一层投影
          {
            map: "JS",
            zlevel: -1,
            zoom: 0.88, //当前视角的缩放比例
            roam: false, //是否开启平游或缩放
            center: undefined,
            show: true,
            label: {
              normal: {
                show: false
              },
              emphasis: {
                show: false
              }
            },
            itemStyle: {
              normal: {
                borderJoin: "round",
                borderColor: "#51cbf8",
                borderWidth: 4,
                areaColor: "rgba(128, 128, 128, 0)",
                shadowColor: "#51cbf8",
                shadowOffsetX: 1,
                shadowOffsetY: 1,
                shadowBlur: 25
              },
              emphasis: {
                show: false
              }
            }
          },
          // 第二层投影
          {
            map: "JS",
            zlevel: -2,
            zoom: 0.89, //当前视角的缩放比例
            roam: false, //是否开启平游或缩放
            center: undefined,
            show: true,
            label: {
              normal: {
                show: false
              },
              emphasis: {
                show: false
              }
            },
            itemStyle: {
              normal: {
                borderJoin: "round",
                borderColor: "#1275cb",
                borderWidth: 4,
                areaColor: "rgba(128, 128, 128, 0)",
                shadowColor: "#1275cb",
                shadowOffsetX: 1,
                shadowOffsetY: 1
              },

              emphasis: {
                show: false
              }
            }
          }
        ]
      },
      // 地图上显示的数据
      mapData: {
        geoCoordMap: {
          啦啦啦: [116.901166, 39.424846, 850],
          哈哈哈: [117.015998, 39.368917, 850],
          嘻嘻嘻: [117.103727, 39.486222, 850]
        },
        projectData: [
          {
            name: "啦啦啦",
            value: (Math.random() * 300).toFixed(2),
            projectName: "人工智能园区"
          },
          {
            name: "哈哈哈",
            value: (Math.random() * 300).toFixed(2),
            projectName: "OA系统"
          },
          {
            name: "嘻嘻嘻",
            value: (Math.random() * 300).toFixed(2),
            projectName: "项目管理系统 \n项目管理系统"
          }
        ]
      }
    };
  },
  mounted() {
    var that = this;
    that.chart = echarts.init(document.getElementById("mapEchart"));
    echarts.registerMap("JS", geoJson);
    that.chart.setOption(that.option);
    that.chart.on("click", function(param) {
      if (that.mapflag) {
        that.mapflag = false;
        // 执行
        var mapName = param.name.toString();
        that.titleText = mapName;
        //清除上一个实例
        setTimeout(() => {
          that.chart.dispose();
        }, 10);
        setTimeout(() => {
          that.chart = echarts.init(document.getElementById("mapEchart"));
          that.registerAndsetOption(
            that.chart,
            "1212",
            mapName,
            tianjinmap[mapName]
          );
          that.initEcharts(that.chart, mapName);
        }, 50);
        // 节流
        setTimeout(() => {
          that.mapflag = true;
        }, 2000);
      }
    });
  },
  methods: {
    // 地图
    /**
     *@param {object} myChart实例
     *@param {string} id
     *@param {string} Json数据
     * */
    registerAndsetOption(myChart, id, name, mapJson, flag) {
      this.labelShow = false;
      echarts.registerMap(name, mapJson);
      myChart.setOption({
        title: {
          // 标题
          top: "5%",
          text: `${this.titleText}3D地图`,
          subtext: "",
          x: "center",
          textStyle: {
            color: "#fff"
          }
        },
        label: {
          // 标签的相关设置
          show: false, // (地图上的城市名称)是否显示标签 [ default: false ]
          distance: 10, // 标签距离图形的距离，在三维的散点图中这个距离是屏幕空间的像素值，其它图中这个距离是相对的三维距离
          // 标签内容格式器
          textStyle: {
            // 标签的字体样式
            color: "#000", // 地图初始化区域字体颜色
            fontSize: 18 // 字体大小
          }
        },
        series: [
          {
            type: "map3D",
            map: name,
            name: "map3D",
            boxHeight: 10,
            silent: true,
            regionHeight: 10, // 三维高度
            // map: "tongren",
            itemStyle: {
              // 三维地理坐标系组件 中三维图形的视觉属性，包括颜色，透明度，描边等。
              show: false,
              color: "#15559a",
              // 地图板块的颜色
              opacity: 50, // 图形的不透明度 [ default: 1 ]
              borderWidth: 1, // (地图板块间的分隔线)图形描边的宽度。加上描边后可以更清晰的区分每个区域   [ default: 0 ]
              borderColor: "#37F6E0" // 图形描边的颜色。[ default: #333 ]
            },
            label: {
              // 标签的相关设置
              show: true, // (地图上的城市名称)是否显示标签 [ default: false ]
              distance: 50,
              formatter(param) {
                const city = param.name.substr(0);
                return `{sty1|${city}}`;
              },
              rich: {
                sty1: {
                  color: "#ffffff",
                  // fontSize: this.getSize(0.1),
                  fontSize: 15,
                  align: "center"
                }
              },
              textStyle: {
                // 标签的字体样式
                color: "#ffffff", // 地图初始化区域字体颜色
                opacity: 1 // 字体透明度
              }
            },
            light: {
              // 光照相关的设置。在 shading 为 'color' 的时候无效。  光照的设置会影响到组件以及组件所在坐标系上的所有图表。合理的光照设置能够让整个场景的明暗变得更丰富，更有层次。
              main: {
                show: false,
                // 场景主光源的设置，在 globe 组件中就是太阳光。
                color: "#fff", //主光源的颜色。[ default: #fff ]
                intensity: 1, //主光源的强度。[ default: 1 ]
                shadow: true, //主光源是否投射阴影。默认关闭。    开启阴影可以给场景带来更真实和有层次的光照效果。但是同时也会增加程序的运行开销。
                shadowQuality: "high", // 阴影的质量。可选'low', 'medium', 'high', 'ultra' [ default: 'medium' ]
                alpha: 55, // 主光源绕 x 轴，即上下旋转的角度。配合 beta 控制光源的方向。[ default: 40 ]
                beta: 10 // 主光源绕 y 轴，即左右旋转的角度。[ default: 40 ]
              },
              ambient: {
                // 全局的环境光设置。
                color: "#fff", // 环境光的颜色。[ default: #fff ]
                intensity: 0.2 // 环境光的强度。[ default: 0.2 ]
              }
            },
            groundPlane: {
              // 地面可以让整个组件有个“摆放”的地方，从而使整个场景看起来更真实，更有模型感。
              show: false // 是否显示地面。[ default: false ]
              // color: "#aaa", // 地面颜色。[ default: '#aaa' ]
            },
            universalTransition: true,
            viewControl: {
              // 用于鼠标的旋转，缩放等视角控制。
              projection: "perspective", // 投影方式，默认为透视投影'perspective'，也支持设置为正交投影'orthographic'。
              autoRotate: true, // 是否开启视角绕物体的自动旋转查看。[ default: false ]
              autoRotateDirection: "cw", // 物体自传的方向。默认是 'cw' 也就是从上往下看是顺时针方向，也可以取 'ccw'，既从上往下看为逆时针方向。
              autoRotateSpeed: 10, // 物体自传的速度。单位为角度 / 秒，默认为10 ，也就是36秒转一圈。
              autoRotateAfterStill: 0.1, // 在鼠标静止操作后恢复自动旋转的时间间隔。在开启 autoRotate 后有效。[ default: 3 ]
              damping: 0, // 鼠标进行旋转，缩放等操作时的迟滞因子，在大于等于 1 的时候鼠标在停止操作后，视角仍会因为一定的惯性继续运动（旋转和缩放）。[ default: 0.8 ]
              rotateSensitivity: 10, // 旋转操作的灵敏度，值越大越灵敏。支持使用数组分别设置横向和纵向的旋转灵敏度。默认为1, 设置为0后无法旋转。	rotateSensitivity: [1, 0]——只能横向旋转； rotateSensitivity: [0, 1]——只能纵向旋转。
              zoomSensitivity: 10, // 缩放操作的灵敏度，值越大越灵敏。默认为1,设置为0后无法缩放。
              panSensitivity: 50, // 平移操作的灵敏度，值越大越灵敏。默认为1,设置为0后无法平移。支持使用数组分别设置横向和纵向的平移灵敏度
              panMouseButton: "left", // 平移操作使用的鼠标按键，支持：'left' 鼠标左键（默认）;'middle' 鼠标中键 ;'right' 鼠标右键(注意：如果设置为鼠标右键则会阻止默认的右键菜单。)
              rotateMouseButton: "left", // 旋转操作使用的鼠标按键，支持：'left' 鼠标左键;'middle' 鼠标中键（默认）;'right' 鼠标右键(注意：如果设置为鼠标右键则会阻止默认的右键菜单。)
              distance: 150, // [ default: 100 ] 默认视角距离主体的距离，对于 grid3D 和 geo3D 等其它组件来说是距离中心原点的距离,对于 globe 来说是距离地球表面的距离。在 projection 为'perspective'的时候有效。
              minDistance: 40, // [ default: 40 ] 视角通过鼠标控制能拉近到主体的最小距离。在 projection 为'perspective'的时候有效。
              maxDistance: 800, // [ default: 400 ] 视角通过鼠标控制能拉远到主体的最大距离。在 projection 为'perspective'的时候有效。
              alpha: 25, // 视角绕 x 轴，即上下旋转的角度。配合 beta 可以控制视角的方向。[ default: 40 ]
              beta: 0, // 视角绕 y 轴，即左右旋转的角度。[ default: 0 ]
              minAlpha: -360, // 上下旋转的最小 alpha 值。即视角能旋转到达最上面的角度。[ default: 5 ]
              maxAlpha: 360, // 上下旋转的最大 alpha 值。即视角能旋转到达最下面的角度。[ default: 90 ]
              minBeta: -360, // 左右旋转的最小 beta 值。即视角能旋转到达最左的角度。[ default: -80 ]
              maxBeta: 360, // 左右旋转的最大 beta 值。即视角能旋转到达最右的角度。[ default: 80 ]
              center: [0, 0, 0], // 视角中心点，旋转也会围绕这个中心点旋转，默认为[0,0,0]。
              animation: false, // 是否开启动画。[ default: true ]
              animationDurationUpdate: 1000, // 过渡动画的时长。[ default: 1000 ]
              animationEasingUpdate: "cubicInOut" // 过渡动画的缓动效果。[ default: cubicInOut ]
            },
            data: mapJson.name
          }
          // {
          //   type: "lines",
          //   coordinateSystem: "geo",
          //   zlevel: 5,
          //   // effect: {
          //   //   show: true,
          //   //   period: 2, //箭头指向速度，值越小速度越快
          //   //   trailLength: 0.2, //特效尾迹长度[0,1]值越大，尾迹越长重
          //   //   symbol: "triangle", //箭头图标
          //   //   symbolSize: 5, //图标大小
          //   //   color: "#37F6E0", // 图标颜色
          //   // },
          //   lineStyle: {
          //     normal: {
          //       // type: [5, 5],
          //       dashOffset: 5,
          //       show: true,
          //       width: 2, //尾迹线条宽度
          //       opacity: 1, //尾迹线条透明度
          //       curveness: 0, //尾迹线条曲直度
          //       color: "#37F6E0" // 飞线颜色
          //     },
          //     color: "#37F6E0"
          //   },
          //   label: {
          //     show: true,
          //     position: "start",
          //     formatter: function(params) {
          //       //文本提示框
          //       return "{title|" + "武清区：" + "}{value|" + "120" + "}";
          //     },
          //     width: 130,
          //     height: 45,
          //     lineHeight: 45,
          //     // backgroundColor: {
          //     //   image: ydx
          //     // },
          //     rich: {
          //       //标题样式
          //       title: {
          //         height: 20,
          //         width: 60,
          //         align: "center",
          //         fontSize: 18,
          //         color: "#A7D4C6"
          //       },
          //       value: {
          //         //内容样式
          //         height: 20,
          //         width: 20,
          //         align: "center",
          //         fontSize: 18,
          //         color: "#00FFB5"
          //       }
          //     }
          //   },

          //   data: [
          //     {
          //       coords: [
          //         [117.00553, 39.48815], // 起点
          //         [117.04443, 39.38415] // 终点
          //       ]
          //     }
          //   ]
          // }
        ]
      });
    },
    /**转化已有数据到地图上显示的配置数据
     * @param {object} 插排数据
     * */
    convertData(data) {
      const res = [];
      for (let i = 0; i < data.length; i++) {
        const geoCoord = this.mapData.geoCoordMap[data[i].name];
        if (geoCoord) {
          res.push({
            name: data[i].name,
            value: geoCoord.concat(data[i].value),
            projectName: data[i].projectName,
            label: {
              show: false,
              position: "top",
              distance: -5,
              formatter(param) {
                // return `{sty1|${param.data.projectName}}`;
                return `XXX项目`;
              },
              rich: {
                sty1: {
                  padding: this.getSize(0.07),
                  backgroundColor: "rgba(7, 28, 38, 0.7)",
                  borderWidth: 1,
                  borderColor: "#FF771A",
                  borderRadius: 2,
                  fontSize: this.getSize(0.14)
                }
              },
              textStyle: {
                color: "#ffffff"
              }
            },
            emphasis: {
              label: {
                show: true,
                position: "top",
                distance: 0,
                formatter(param) {
                  return `{sty1|${param.data.projectName}}`;
                  // return `XXX项目`;
                },
                rich: {
                  sty1: {
                    padding: 7,
                    backgroundColor: "rgba(7, 28, 38, 0.7)",
                    borderWidth: 0,
                    borderColor: "#FF771A",
                    borderRadius: 2,
                    fontSize: this.getSize(0.14)
                  }
                },
                textStyle: {
                  color: "#ffffff"
                }
              }
            }
          });
        }
      }
      return res;
    },
    // 获取图表配置
    getOptions() {
      var that = this;
      const options = {
        tooltip: {
          show: false,
          trigger: "item"
        },
        textStyle: {
          color: "#ffffff", // 高亮时标签颜色变为 白色
          fontSize: 20 // 高亮时标签字体 变大
        },
        // 地图主要配置
        geo3D: {
          show: true,
          map: "map",
          name: "map",
          top: this.setting.top,
          right: this.setting.right,
          left: "0%",
          bottom: 0,
          // 清除高亮
          silent: true,
          color: "#1491a8",
          regionHeight: this.setting.regionHeight, // 三维高度
          shading: "color",
          // colorMaterial: {
          //   // detailTexture: this.mapImg, // 纹理贴图 格式支持（string, HTMLImageElement, HTMLCanvasElement）
          //   detailTexture: ``,
          //   textureTiling: 1 // 纹理平铺，1是拉伸，数字表示纹理平铺次数
          // },
          label: {
            // 标签的相关设置
            show: true, // (地图上的城市名称)是否显示标签 [ default: false ]
            distance: 10,
            formatter(param) {
              const city = param.name.substr(0, 2);
              return `{sty1|${city}}`;
            },
            rich: {
              sty1: {
                color: "#ffffff",
                fontSize: this.getSize(0.14),
                align: "center"
              }
            },
            textStyle: {
              // 标签的字体样式
              color: "#ffffff", // 地图初始化区域字体颜色
              opacity: 1 // 字体透明度
            }
          },
          itemStyle: {
            // 三维地理坐标系组件 中三维图形的视觉属性，包括颜色，透明度，描边等。
            color: "#004A80", // 地图板块的颜色
            // 背景rgb(0 82 181)
            // color: "#719edd",
            opacity: 10, // 图形的不透明度 [ default: 1 ]
            borderWidth: 1, // (地图板块间的分隔线)图形描边的宽度。加上描边后可以更清晰的区分每个区域 [ default: 0 ]
            borderColor: "#0eb0c9", // 图形描边的颜色。[ default: #333 ]
            areaColor: "#1491a8"
          },
          emphasis: {
            // 鼠标 hover 高亮时图形和标签的样式 (当鼠标放上去时 label和itemStyle 的样式)
            label: {
              // label高亮时的配置
              show: true,
              textStyle: {
                color: "#fff", // 高亮时标签颜色变为 白色
                fontSize: this.getSize(0.15) // 高亮时标签字体 变大
              }
            }
          },
          light: {
            // 光照相关的设置。在 shading 为 'color' 的时候无效。
            // 光照的设置会影响到组件以及组件所在坐标系上的所有图表。合理的光照设置能够让整个场景的明暗变得更丰富，更有层次。
            main: {
              // 场景主光源的设置，在 globe 组件中就是太阳光。
              color: "#ffeeee", // 主光源的颜色。[ default: #fff ]
              intensity: 1.1, // 主光源的强度。[ default: 1 ]
              shadow: false, // 主光源是否投射阴影。默认关闭。 开启阴影可以给场景带来更真实和有层次的光照效果。但是同时也会增加程序的运行开销。
              alpha: 55, // 主光源绕 x 轴，即上下旋转的角度。配合 beta 控制光源的方向。[ default: 40 ]
              beta: 10 // 主光源绕 y 轴，即左右旋转的角度。[ default: 40 ]
            },
            ambient: {
              // 全局的环境光设置。
              color: "#fff", // 环境光的颜色。[ default: #fff ]
              intensity: 0.1 // 环境光的强度。[ default: 0.2 ]
            }
          },
          viewControl: {
            // 用于鼠标的旋转，缩放等视角控制。
            projection: "perspective", // 投影方式，默认为透视投影'perspective'，也支持设置为正交投影'orthographic'。
            autoRotate: false, // 是否开启视角绕物体的自动旋转查看。[ default: false ]
            autoRotateDirection: "cw", // 物体自传的方向。默认是 'cw' 也就是从上往下看是顺时针方向，也可以取 'ccw'，既从上往下看为逆时针方向。
            autoRotateSpeed: 10, // 物体自传的速度。单位为角度 / 秒，默认为10 ，也就是36秒转一圈。
            autoRotateAfterStill: 3, // 在鼠标静止操作后恢复自动旋转的时间间隔。在开启 autoRotate 后有效。[ default: 3 ]
            damping: 0, // 鼠标进行旋转，缩放等操作时的迟滞因子，在大于等于 1 的时候鼠标在停止操作后，视角仍会因为一定的惯性继续运动（旋转和缩放）。[ default: 0.8 ]
            rotateSensitivity: this.setting.Sensitivity, // 旋转操作的灵敏度，值越大越灵敏。支持使用数组分别设置横向和纵向的旋转灵敏度。默认为1, 设置为0后无法旋转。	rotateSensitivity: [1, 0]——只能横向旋转； rotateSensitivity: [0, 1]——只能纵向旋转。
            zoomSensitivity: this.setting.Sensitivity, // 缩放操作的灵敏度，值越大越灵敏。默认为1,设置为0后无法缩放。
            panSensitivity: this.setting.Sensitivity, // 平移操作的灵敏度，值越大越灵敏。默认为1,设置为0后无法平移。支持使用数组分别设置横向和纵向的平移灵敏度
            panMouseButton: "left", // 平移操作使用的鼠标按键，支持：'left' 鼠标左键（默认）;'middle' 鼠标中键 ;'right' 鼠标右键(注意：如果设置为鼠标右键则会阻止默认的右键菜单。)
            rotateMouseButton: "left", // 旋转操作使用的鼠标按键，支持：'left' 鼠标左键;'middle' 鼠标中键（默认）;'right' 鼠标右键(注意：如果设置为鼠标右键则会阻止默认的右键菜单。)
            distance: this.setting.distance, // [ default: 100 ] 默认视角距离主体的距离，对于 grid3D 和 geo3D 等其它组件来说是距离中心原点的距离,对于 globe 来说是距离地球表面的距离。在 projection 为'perspective'的时候有效。
            minDistance: 40, // [ default: 40 ] 视角通过鼠标控制能拉近到主体的最小距离。在 projection 为'perspective'的时候有效。
            maxDistance: 800, // [ default: 400 ] 视角通过鼠标控制能拉远到主体的最大距离。在 projection 为'perspective'的时候有效。
            alpha: 30, // 视角绕 x 轴，即上下旋转的角度。配合 beta 可以控制视角的方向。[ default: 40 ]
            beta: -0, // 视角绕 y 轴，即左右旋转的角度。[ default: 0 ]
            minAlpha: 10, // 上下旋转的最小 alpha 值。即视角能旋转到达最上面的角度。[ default: 5 ]
            maxAlpha: 40, // 上下旋转的最大 alpha 值。即视角能旋转到达最下面的角度。[ default: 90 ]
            minBeta: -360, // 左右旋转的最小 beta 值。即视角能旋转到达最左的角度。[ default: -80 ]
            maxBeta: 360, // 左右旋转的最大 beta 值。即视角能旋转到达最右的角度。[ default: 80 ]
            center: [0, 0, 0], // 视角中心点，旋转也会围绕这个中心点旋转，默认为[0,0,0]。
            animation: false, // 是否开启动画。[ default: true ]
            animationDurationUpdate: 1000, // 过渡动画的时长。[ default: 1000 ]
            animationEasingUpdate: "cubicInOut" // 过渡动画的缓动效果。[ default: cubicInOut ]
          }
        },
        // 关键数据
        series: [
          // 叠加地图上需要显示的数据，插牌
          {
            type: "scatter3D",
            name: "scatter3D",
            coordinateSystem: "geo3D",
            symbol:
              "path://M262.775253 0m64 0l0 0q64 0 64 64l0 896q0 64-64 64l0 0q-64 0-64-64l0-896q0-64 64-64Z",
            // 柱子宽高
            symbolSize: [15, 158],
            itemStyle: {
              color: "#248067",
              opacity: 1
            },
            data: this.convertData(this.mapData.projectData)
          },
          // 使用和geo3d的配置数据，叠加一层暗沉的厚度
          {
            name: "map3D", // series名称
            type: "map3D", // series图表类型
            map: "map",
            top: 0,
            right: this.setting.right,
            left: "0%",
            bottom: 0,
            regionHeight: this.setting.regionHeight - 0.1, // 三维高度
            silent: true,
            label: {
              // 标签的相关设置
              show: false // (地图上的城市名称)是否显示标签 [ default: false ]
            },
            itemStyle: {
              // 三维地理坐标系组件 中三维图形的视觉属性，包括颜色，透明度，描边等。
              color: "#4888e4", // 地图板块的颜色
              opacity: 1, // 图形的不透明度 [ default: 1 ]
              borderWidth: 0, // (地图板块间的分隔线)图形描边的宽度。加上描边后可以清晰的区分每个区域
              borderColor: "#A74A11" // 图形描边的颜色。[ default: #333 ]
            },
            emphasis: {
              // 鼠标hover 高亮时图形和标签的样式 (当鼠标放上去时 label和itemStyle 的样式)
              label: {
                // label高亮时的配置
                show: true
              }
            },
            groundPlane: {
              // 地面可以让整个组件有个“摆放”的地方，从而使整个场景看起来更真实，更有模型感。
              show: false // 是否显示地面。[ default: false ]
              // color: "#aaa", // 地面颜色。[ default: '#aaa' ]
            },

            viewControl: {
              // 用于鼠标的旋转，缩放等视角控制。
              projection: "perspective", // 投影方式，默认为透视投影'perspective'，也支持设置为正交投影'orthographic'。
              autoRotate: false, // 是否开启视角绕物体的自动旋转查看。[ default: false ]
              autoRotateDirection: "cw", // 物体自传的方向。默认是 'cw' 也就是从上往下看是顺时针方向，也可以取 'ccw'，既从上往下看为逆时针方向。
              autoRotateSpeed: 10, // 物体自传的速度。单位为角度 / 秒，默认为10 ，也就是36秒转一圈。
              autoRotateAfterStill: 3, // 在鼠标静止操作后恢复自动旋转的时间间隔。在开启 autoRotate 后有效。[ default: 3 ]
              damping: 0, // 鼠标进行旋转，缩放等操作时的迟滞因子，在大于等于 1 的时候鼠标在停止操作后，视角仍会因为一定的惯性继续运动（旋转和缩放）。[ default: 0.8 ]
              rotateSensitivity: this.setting.Sensitivity, // 旋转操作的灵敏度，值越大越灵敏。支持使用数组分别设置横向和纵向的旋转灵敏度。默认为1, 设置为0后无法旋转。	rotateSensitivity: [1, 0]——只能横向旋转； rotateSensitivity: [0, 1]——只能纵向旋转。
              zoomSensitivity: this.setting.Sensitivity, // 缩放操作的灵敏度，值越大越灵敏。默认为1,设置为0后无法缩放。
              panSensitivity: this.setting.Sensitivity, // 平移操作的灵敏度，值越大越灵敏。默认为1,设置为0后无法平移。支持使用数组分别设置横向和纵向的平移灵敏度
              panMouseButton: "left", // 平移操作使用的鼠标按键，支持：'left' 鼠标左键（默认）;'middle' 鼠标中键 ;'right' 鼠标右键(注意：如果设置为鼠标右键则会阻止默认的右键菜单。)
              rotateMouseButton: "left", // 旋转操作使用的鼠标按键，支持：'left' 鼠标左键;'middle' 鼠标中键（默认）;'right' 鼠标右键(注意：如果设置为鼠标右键则会阻止默认的右键菜单。)
              distance: this.setting.distance, // [ default: 100 ] 默认视角距离主体的距离，对于 grid3D 和 geo3D 等其它组件来说是距离中心原点的距离,对于 globe 来说是距离地球表面的距离。在 projection 为'perspective'的时候有效。
              minDistance: 40, // [ default: 40 ] 视角通过鼠标控制能拉近到主体的最小距离。在 projection 为'perspective'的时候有效。
              maxDistance: 800, // [ default: 400 ] 视角通过鼠标控制能拉远到主体的最大距离。在 projection 为'perspective'的时候有效。
              alpha: 30, // 视角绕 x 轴，即上下旋转的角度。配合 beta 可以控制视角的方向。[ default: 40 ]
              beta: -0, // 视角绕 y 轴，即左右旋转的角度。[ default: 0 ]
              minAlpha: 10, // 上下旋转的最小 alpha 值。即视角能旋转到达最上面的角度。[ default: 5 ]
              maxAlpha: 40, // 上下旋转的最大 alpha 值。即视角能旋转到达最下面的角度。[ default: 90 ]
              minBeta: -360, // 左右旋转的最小 beta 值。即视角能旋转到达最左的角度。[ default: -80 ]
              maxBeta: 360, // 左右旋转的最大 beta 值。即视角能旋转到达最右的角度。[ default: 80 ]
              center: [0, 0, 0], // 视角中心点，旋转也会围绕这个中心点旋转，默认为[0,0,0]。
              animation: false, // 是否开启动画。[ default: true ]
              animationDurationUpdate: 1000, // 过渡动画的时长。[ default: 1000 ]
              animationEasingUpdate: "cubicInOut" // 过渡动画的缓动效果。[ default: cubicInOut ]
            }
          }
          // 白模
        ]
      };
      return options;
    },
    // 适配不同尺寸浏览器，避免地图上的字号过小或过大
    getSize(res) {
      const docEl = document.documentElement;
      const clientWidth =
        window.innerWidth ||
        document.documentElement.clientWidth ||
        document.body.clientWidth;
      if (!clientWidth) return;
      const fontSize = 100 * (clientWidth / 1920);
      return res * fontSize;
    },
    // 初始化
    initEcharts(myEcharts, mapName) {
      const that = this;
      myEcharts.showLoading({
        animation: true,
        animationDuration: 50000,
        maskColor: "rgba(0, 0, 0, 0.2)"
      });
      echarts.registerMap("map", tianjinmap[`${mapName}`]);
      myEcharts.hideLoading();
      myEcharts.setOption(this.getOptions());
    }
  }
};
</script>

<style>
body {
  background-image: url("../../assets/beijingtupian.png");
}
</style>

<style scoped>
.title {
  width: 100%;
  height: 10vh;
  text-align: center;
  color: #fff;
  font-size: 2em;
  line-height: 10vh;
}
.box {
  position: absolute;
  width: 90%;
  height: 80vh;
  left: 5%;
  top: 10%;
  background-color: rgba(24, 27, 52, 0.62);
}

.chart {
  position: relative;
  height: 90%;
  top: 10%;
}
.backBtn {
  position: absolute;
  top: 0;
  background-color: #00c298;
  border: 0;
  color: #fff;
  height: 27px;
  font-family: Microsoft Yahei;
  font-size: 1em;
  cursor: pointer;
}
.myBlog {
  position: absolute;
  top: 2px;
  right: 5%;
  display: block;
  border: 2px solid #262a53;
}
.myBlog a {
  text-decoration: none;
  display: inline-block;
  color: #fff;
  padding: 5px;
  font-size: 1em;
}

.myBlog a img {
  vertical-align: middle;
  height: 20px;
  width: 20px;
  border-radius: 50%;
  margin: -5px 5px auto auto;
}
.bottom {
  position: absolute;
  width: 100%;
  height: 5%;
  line-height: 100%;
  left: 0;
  bottom: 0%;
  text-align: center;
}
</style>
