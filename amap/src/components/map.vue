<template>
  <div id="container" class="map"></div>
</template>

<script>
import { constants } from "crypto";
import { stringify } from "querystring";
export default {
  name: "AMap",
  props: {},
  data() {
    return {
      map: {},
      buildingLayer: {},
      object3Dlayer: {},
      radar: {}
    };
  },
  methods: {
    initMap() {
      this.buildingLayer = new AMap.Buildings({
        zIndex: 10,
        merge: false,
        sort: false,
        zooms: [10, 20],
        heightFactor: 2
      });
      this.map = new AMap.Map("container", {
        center: [117.120406, 36.657127],
        viewMode: "3D",
        buildingAnimation: true,
        pitch: 60,
        rotation: 0,
        features: ["bg", "road", "point"],
        mapStyle: "amap://styles/dark",
        //mapStyle: "amap://styles/6dfae7615792de8ce8d9605e06836a39",
        layers: [new AMap.TileLayer(), this.buildingLayer],
        zoom: 16
      });

      this.object3Dlayer = new AMap.Object3DLayer();
      this.map.add(this.object3Dlayer);
      this.map.on("dblclick", e => {
        this.buildRadar();
      });
    },
    setBuilding() {
      var options = {
        hideWithoutStyle: true,
        areas: [
          {
            visible: true,
            rejectTexture: true,
            color1: "eebbbbbb",
            color2: "ff666666",
            path: [
              [117.111272, 36.665097],
              [117.099513, 36.664959],
              [117.098998, 36.656972],
              [117.101573, 36.657316],
              [117.100972, 36.645472],
              [117.109641, 36.645335],
              [117.116164, 36.647676],
              [117.120456, 36.648227],
              [117.127065, 36.647676],
              [117.13333, 36.646574],
              [117.132815, 36.653873],
              [117.134189, 36.658693],
              [117.134516, 36.661787],
              [117.133443, 36.66313],
              [117.130567, 36.663268],
              [117.129323, 36.665161],
              [117.126834, 36.665712],
              [117.120096, 36.665126],
              [117.119881, 36.662958],
              [117.111272, 36.665097]
            ]
          }
        ]
      };
      this.buildingLayer.setStyle(options);
      new AMap.Polygon({
        bubble: true,
        fillOpacity: 0,
        strokeWeight: 1,
        path: options.areas[0].path,
        map: this.map
      });
    },
    buildRadar() {
      this.radar = new AMap.Object3D.Mesh();
      this.radar.transparent = true;
      this.radar.backOrFront = "front";

      var geometry = this.radar.geometry;
      var radius = 800; // 米
      radius = radius / this.map.getResolution(this.map.getCenter(), 20);
      var unit = 1;
      var range = 300;
      var count = range / unit;

      for (var i = 0; i < count; i += 1) {
        var angle1 = (i * unit * Math.PI) / 180;
        var angle2 = ((i + 1) * unit * Math.PI) / 180;

        var p1x = Math.cos(angle1) * radius;
        var p1y = Math.sin(angle1) * radius;
        var p2x = Math.cos(angle2) * radius;
        var p2y = Math.sin(angle2) * radius;

        geometry.vertices.push(0, 0, 0);
        geometry.vertices.push(p1x, p1y, 0);
        geometry.vertices.push(p2x, p2y, 0);

        var opacityStart = this.getOpacity(i / count);
        var opacityEnd = this.getOpacity((i + 1) / count);

        geometry.vertexColors.push(0, 1, 0.2, opacityStart);
        geometry.vertexColors.push(0, 1, 0.2, opacityStart);
        geometry.vertexColors.push(0, 1, 0.2, opacityEnd);
      }
      this.radar.position(this.map.getCenter());
      this.object3Dlayer.add(this.radar);
      this.scan();
    },
    getOpacity(scale) {
      return 1 - Math.pow(scale, 0.1);
    },
    scan() {
      this.radar.rotateZ(-2);
      AMap.Util.requestAnimFrame(this.scan);
    },
    headMap() {
      let heatmapData = [];
      let center = this.map.getCenter();
      // for (let index = 0; index < 100; index++) {
      //   let offset = Math.random() * 0.03;
      //   let point = {
      //     lng: 117.109468 + offset,
      //     lat: 36.659734,
      //     count: offset * 10000
      //   };
      //   heatmapData.push(point);
      // }
      [
        { lng: 117.115168, lat: 36.658836, count: 130 },
        { lng: 117.115399, lat: 36.658892, count: 130 },
        { lng: 117.115511, lat: 36.658866, count: 120 },
        { lng: 117.115608, lat: 36.658746, count: 150 },
        { lng: 117.115645, lat: 36.658578, count: 100 },
        { lng: 117.117397, lat: 36.657246, count: 90 },
        { lng: 117.117324, lat: 36.657428, count: 100 },
        { lng: 117.11718, lat: 36.657587, count: 100 },
        { lng: 117.117039, lat: 36.657681, count: 100 },
        { lng: 117.116819, lat: 36.657816, count: 100 },
        { lng: 117.116694, lat: 36.65787, count: 100 },
        { lng: 117.116486, lat: 36.65787, count: 100 },
        { lng: 117.116145, lat: 36.657857, count: 70 },
        { lng: 117.115844, lat: 36.657775, count: 100 },
        { lng: 117.115666, lat: 36.657667, count: 100 },
        { lng: 117.115518, lat: 36.657574, count: 80 },
        { lng: 117.115433, lat: 36.657455, count: 100 },
        { lng: 117.115337, lat: 36.657324, count: 100 },
        { lng: 117.115271, lat: 36.657194, count: 100 },
        { lng: 117.11514, lat: 36.656777, count: 100 },
        { lng: 117.115146, lat: 36.656579, count: 100 },
        { lng: 117.115166, lat: 36.656385, count: 40 },
        { lng: 117.115211, lat: 36.656206, count: 100 },
        { lng: 117.115283, lat: 36.65603, count: 60 },
        { lng: 117.115408, lat: 36.655856, count: 100 },
        { lng: 117.115547, lat: 36.655674, count: 100 },
        { lng: 117.115986, lat: 36.655539, count: 100 },
        { lng: 117.116286, lat: 36.655517, count: 100 },
        { lng: 117.11666, lat: 36.655539, count: 100 },
        { lng: 117.117042, lat: 36.655629, count: 60 },
        { lng: 117.117256, lat: 36.655822, count: 100 },
        { lng: 117.117501, lat: 36.656006, count: 100 },
        { lng: 117.1176, lat: 36.65617, count: 100 },
        { lng: 117.123885, lat: 36.659393, count: 100 },
        { lng: 117.123863, lat: 36.659281, count: 100 },
        { lng: 117.123859, lat: 36.659225, count: 100 },
        { lng: 117.123851, lat: 36.659136, count: 100 },
        { lng: 117.123866, lat: 36.659014, count: 100 },
        { lng: 117.123859, lat: 36.658926, count: 100 },
        { lng: 117.123889, lat: 36.658836, count: 100 },
        { lng: 117.123919, lat: 36.658748, count: 100 },
        { lng: 117.12395, lat: 36.65867, count: 100 },
        { lng: 117.12398, lat: 36.658583, count: 100 },
        { lng: 117.115566, lat: 36.658384, count: 100 },
        { lng: 117.115533, lat: 36.658321, count: 100 },
        { lng: 117.11551, lat: 36.65824, count: 100 },
        { lng: 117.115443, lat: 36.658142, count: 100 },
        { lng: 117.115443, lat: 36.65807, count: 100 },
        { lng: 117.115443, lat: 36.657989, count: 100 },
        { lng: 117.115443, lat: 36.657944, count: 100 },
        { lng: 117.115611, lat: 36.657953, count: 100 },
        { lng: 117.115667, lat: 36.658061, count: 100 },
        { lng: 117.115701, lat: 36.65816, count: 100 },
        { lng: 117.115712, lat: 36.658294, count: 100 },
        { lng: 117.115712, lat: 36.658411, count: 100 }
      ].forEach(item => heatmapData.push(item));
      let heatmapOpts = {
        //3d 相关的参数
        "3d": {
          //热度转高度的曲线控制参数，可以利用左侧的控制面板获取
          heightBezier: [0.4, 0.2, 0.4, 0.8],
          //取样精度，值越小，曲面效果越精细，但同时性能消耗越大
          gridSize: 2,
          heightScale: 1
        }
      };

      //初始化heatmap对象
      let heatmap = new AMap.Heatmap(this.map, heatmapOpts);

      heatmap.setDataSet({
        data: heatmapData,
        max: 100
      });
    }
  },
  mounted() {
    this.initMap();
    this.setBuilding();

    this.headMap();
  },
  created() {}
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>
.map {
  width: 100vw;
  height: 100vh;
}
</style>

