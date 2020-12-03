<template>
  <div class="searchDiv">
    <input type="text" class="mapsearch" @keyup.enter="search" v-model="searchKey" placeholder="搜索公园、景区"/>
    <!-- <div id="searchBtn">搜索</div> -->
    <x-button type="primary" action-type="button" id="searchBtn" @click="search">搜索</x-button>
    <!-- 扫一扫 -->
    <div class="sys" @click="wxSys()">
      <img src="../../img/sys.png" alt />
    </div>
    <!-- 列表 -->
    <div class="y-wrap mt10" v-if="showPark">
      <div class="y-title">
        <div>{{title}}</div>
        <div>
          <img src="@/components/img/close.png" style="z-index:999;" @click="showPark=false;" class="cls" alt="">
        </div>
      </div>
      <div class="mt15 y-gylist">
        <el-tabs v-model="activeName" @tab-click="handleClick">
          <el-tab-pane label="公园" name="first">
            <!-- 公园搜索 -->
            <div class="weui-media-box weui-media-box_text"
                 v-for="(i,idx) in parkList"
                 @click="parkDetail(i.geometry.longitude ,i.geometry.latitude)">
              <p class="weui-media-box__title">{{idx+1}}、{{i.attributes.NAME}}<span><!--{{i.distance}}--></span></p>
              <p class="weui-media-box__desc">{{`地址:${i.attributes.ADDRESS}`}}</p>
            </div>
          </el-tab-pane>
          <el-tab-pane label="景区" name="second">
            <!-- 景区搜索 -->
            <div class="weui-media-box weui-media-box_text"
                 v-for="(i,idx) in parkList"
                 @click="parkDetail(i.geometry.longitude ,i.geometry.latitude)">
              <p class="weui-media-box__title">{{idx+1}}、{{i.attributes.name}}<span><!--{{i.distance}}--></span></p>
              <p class="weui-media-box__desc">{{`地址:${i.attributes.address || '暂无数据'}`}}</p>
            </div>
          </el-tab-pane>
        </el-tabs>
      </div>
    </div>
    <!-- 弹框 -->
    <toast
      v-model="showPositionValue"
      type="text"
      :time="3000"
      is-show-mask
      :text="this.text"
      position="middle"
    ></toast>
  </div>
</template>

<script>
/* eslint-disable */
import { XButton, Toast } from "vux";
import wx from "weixin-js-sdk";
import bus from "../../../assets/config/eventBus";
import esriLoader from "esri-loader";
import {OPTION, Park_url, Zt_url} from "../../../assets/config/config";
import SearchPopup from "./searchPopup";
export default {
  components: {
    SearchPopup,
    XButton,
    Toast,
  },
  data() {
    return {
      activeName:"first",
      map: null,
      showPositionValue: false,
      text: null,
      key:'',
      showDetail:false,
      showPark:false,
      showScenery:false,
      title:"搜索结果",
      searchKey:'',
      location:null,//当前坐标
      parkList:[],
      sceneryList:[],
    };
  },
  methods: {
    //授权扫一扫
    wxSys() {
      wx.scanQRCode({
        needResult: 1, // 默认为0，扫描结果由微信处理，1则直接返回扫描结果，
        scanType: ["qrCode"], // 可以指定扫二维码还是一维码，默认二者都有
        success: function (res) {
          //console.log(JSON.stringify(res));
          var result = res.resultStr; // 当needResult 为 1 时，扫码返回的结果
          //console.log("扫描结果：" + result);
          window.location.href = result; //因为我这边是扫描后有个链接，然后跳转到该页面
        },
        error: function (res) {
          this.showPositionValue = true;
          this.text = res;
        },
      });
    },
    async queryZhoubian(){
      const self = this;

      esriLoader.loadModules([
        "esri/tasks/QueryTask",
        "esri/tasks/support/Query",
        "esri/geometry/Polyline",
        "esri/geometry/geometryEngine",
        'esri/geometry/Circle',
        'esri/geometry/Point'
      ]).then(([QueryTask, Query, Polyline, geometryEngine, Circle, Point]) => {
        var queryTask = new QueryTask({
          url: Zt_url + '/3'
        });
        var queryTask2 = new QueryTask({
          url: Zt_url + '/4'
        });
        var query = new Query();
        query.returnGeometry = true;
        query.outFields = ["*"];
        query.start = 0;//开始
        query.num = 2;//多少
        // debugger
        console.log('周边查询半径',this.defaultValue);
        //3公里
        var geo = new Circle({
          "center": new Point({
            "x": 120.663,
            "y": 27.994,
            "spatialReference": {
              wkid: 4326 //84坐标系
            }
          }),
          "radius": 1000 //半径 1Km
        });
        // query.geometry = geo;

        query.where = "1=1";
        queryTask.execute(query).then(function (results) {
          if (results.features && results.features.length) {
            results.features.forEach(function (poi) {
              console.log('公园--Task0--poi',poi.attributes);
            })
          }
        });
        queryTask2.execute(query).then(function (results) {
          if (results.features && results.features.length) {
            results.features.forEach(function (poi) {
              var arr;
              console.log('景区--Task2--poi',poi.attributes);
            })
          }
        });
      });
    },
    async search(){
      const self = this;
      // bus.$emit('enter',self.searchKey)
      // await self.addParkLayer();

      self.showPark = true;
      if(self.activeName === 'first'){
        // self.showScenery=false;
        self.parkList = [];
        self.parkInit();
      }else if (self.activeName === 'second'){
        // self.showPark=false;
        self.parkList = [];
        self.sceneryInit();
      }
    },
    async parkInit() {
       const self = this;

        //http://117.149.228.89:6080/arcgis/rest/services/LY/LYZT/MapServer/3
       esriLoader.loadModules([
         "esri/tasks/QueryTask",
         "esri/tasks/support/Query",
         "esri/geometry/Polyline",
         "esri/geometry/geometryEngine",
         'esri/geometry/Circle',
         'esri/geometry/Point'
       ]).then(([QueryTask, Query, Polyline, geometryEngine, Circle, Point]) => {
         var queryTask = new QueryTask({
           url: "http://117.149.228.89:6080/arcgis/rest/services/LY/LYZT/MapServer/3"
         });
         var query = new Query();
         query.returnGeometry = true;
         query.outFields = ["*"];
         // query.start = 0;//开始
         // query.num = 300;//多少
         if (self.searchKey && self.searchKey != ""){
           query.where = "1 = 1 AND NAME like '%"+self.searchKey+"%'";
         }else {
           query.where = "1 = 1";
         }
         queryTask.execute(query).then(function (results) {
           // console.log('周边查询--公园--Task0-res',results);
           if (results.features && results.features.length) {
             self.parkList = results.features;
           }
         });
       });
      },
    async sceneryInit(){
      const self = this;
      //http://117.149.228.89:6080/arcgis/rest/services/LY/LYZT/MapServer/3
      esriLoader.loadModules([
        "esri/tasks/QueryTask",
        "esri/tasks/support/Query",
        "esri/geometry/Polyline",
        "esri/geometry/geometryEngine",
        'esri/geometry/Circle',
        'esri/geometry/Point'
      ]).then(([QueryTask, Query, Polyline, geometryEngine, Circle, Point]) => {
        const queryTask = new QueryTask({
          url: Zt_url + '/4' //景点
        });
        const query = new Query();
        query.returnGeometry = true;
        query.outFields = ["*"];
        // query.start = 0;//开始
        // query.num = 300;//多少
        if (self.searchKey && self.searchKey != ""){
          query.where = "1 = 1 AND NAME like '%"+self.searchKey+"%'";
        }else {
          query.where = "1 = 1";
        }
        queryTask.execute(query).then(function (results) {
          if (results.features && results.features.length) {
            self.parkList = results.features;
          }
        });
      });
    },
    async addParkLayer(){
      const self = this;
      const temp = {
        id: 2,
        name: "公园",
        url: Zt_url + "/3",
        img: require("../../img/gy.png"),
        fun: "addFeatureayer",
        check: false,
      };
      await self.$parent.$refs.map.addZtLayer(temp);
    },
      //公园详细
    parkDetail(lon,lat) {
        //location.href="/#/park/con";
        // const layer = map.getLayer('garden_PY');
        const self = this;
        esriLoader.loadModules(['esri/Map',
          'esri/Basemap',
          'esri/layers/TileLayer',
          "esri/views/MapView",
          "esri/Graphic"], OPTION)
          .then(([Map, Basemap, TileLayer, MapView, Graphic]) => {
            let position = {
              type: 'point',
              longitude: lon,
              latitude: lat
            };
            const symbol = {
              type: "picture-marker",
              url: require("@/components/img/point.png"), //图片地址
              width: "30px",
              height: "30px",
            };
            let Ghc = new Graphic({
              geometry: position,
              symbol: symbol
            });
            const view = self.$parent.$refs.map.view;
            view.graphics.removeAll(); //清除上一次点击目标
            view.graphics.add(Ghc);
            view.goTo({ zoom: 15, center: [lon, lat] })
              .catch(function (error) {
              if (error.name != "AbortError") {
                console.error(error);
              }});
          });

        // var marker = new self.map.Marker({
        //   position: point,
        //   map: map,
        //   title:p[i].title,
        //   icon:image,
        //   visible:true
        // });
      },
    handleClick(tab, event) {
      const self = this;
      if(self.activeName === 'first'){
        // self.showScenery=false;
        self.parkList = [];
        self.parkInit();
      }else if (self.activeName === 'second'){
        // self.showPark=false;
        self.parkList = [];
        self.sceneryInit();
      }
    }
  },
  mounted() {
    //var that = this;
    //that.$utils.wxgetsign(that); //获取wx.config
  },
};
</script>

<style lang="less" scoped>
@import "../../../assets/config/common.css";
@import url("../../img/css.less");
.searchDiv {
  input {
    outline: none;
    -webkit-appearance: none; /*去除系统默认的样式*/
    -webkit-tap-highlight-color: rgba(0, 0, 0, 0); /* 点击高亮的颜色*/
  }
  .mapsearch {
    width: 79%;
    height: 100%;
    padding: 0% 4%;
    border: 1px solid #ddd;
    border-radius: 8px;
    float: left;
    outline: none;
  }
  .sys {
    position: absolute;
    right: 2%;
    top: 5px;
    width: 23px;
    height: 23px;
    img {
      width: 100%;
    }
  }
  #searchBtn {
    position: absolute;
    top: 1px;
    width: 54px;
    height: 100%;
    right: 12%;
    font-size: 12px;
    color: #fff;
  }
}
.cls{
  position:absolute;right:10px;top:10px;
  width: 18px;
  height: 18px;
}
.el-tabs__header {
  padding: 0;
  position: relative;
  margin: 0 0 !important;
}
</style>
