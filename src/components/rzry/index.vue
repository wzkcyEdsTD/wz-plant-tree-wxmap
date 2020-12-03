<template>
  <div>
    <!-- 地图模块 -->
    <Map id="map" class="map" name="map" ref="map"></Map>
    <!-- 搜索 -->
    <mapSearch ref="search" id="mapSearch" class="mapSearch"></mapSearch>
    <!-- 图层-定位 -->
    <ul id="leftBtn">
      <li @click="a">
        <img :src="require('@/components/img/tc.png')" />
        <p>图层</p>
      </li>
      <li @click="wxGetLocation">
        <img :src="require('@/components/img/dwz.png')" />
        <p>定位</p>
      </li>
    </ul>
    <!-- 成就图标 -->
    <ul class="leftCj">
      <li style="display: flex;flex-flow: column;">
        <img class="icons" src="../img/合欢树.png">
        <img class="icons" src="../img/枇杷树.png">
        <img class="icons" src="../img/白玉兰.png">
      </li>
    </ul>
    <!-- 右按钮 -->
    <ul id="mapBtn">
      <li v-for="item in btn" :key="item.name">
        <!-- @click="()=>$router.push(item.herf)" -->
        <img :src="require('@/components/img/'+item.url+'.png')" @click="jump(item)" />
        <p>{{item.name}}</p>
      </li>
    </ul>
    <!-- 右侧弹框 -->
    <rightPop id="rightPop" class="rightPop" @mapSwitch="mapSwitch" @mapZt="mapZt"></rightPop>
    <!-- 弹幕滚轮 -->
    <Marquee id="barrageDiv" :item-height="80" :interval="10000">
      <Marquee-item v-for="item in barrage" :key="item.id" class="align-middle">
        <img src="../img/mar.png" style="position: absolute;
    top: -12px;" />
        <span style="margin-left:40px">{{item.text}}</span>
      </Marquee-item>
    </Marquee>
    <!-- 弹框 -->
    <detailPopup id="detailPopup" class="detailPopup"></detailPopup>
    <!-- 列表弹框 -->
    <listPopup id="listPopup" class="listPopup"></listPopup>

    <!-- 报错弹框 -->
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
import bus from "../../assets/config/eventBus";

import Map from "../map/map";
import mapSearch from "./map/search";
import detailPopup from "./map/detailPopup";
import listPopup from "./map/listPopup";
import rightPop from "./map/rightPop";
import { Marquee, MarqueeItem, Toast } from "vux";
import { url, inde_url } from "../../assets/config/config";

import wx from "weixin-js-sdk";
// var graphicLayer,map=null;
export default {
  components: {
    Map,
    mapSearch,
    Marquee,
    MarqueeItem,
    detailPopup,
    listPopup,
    rightPop,
    Toast,
  },
  data() {
    return {
      popShow: false,
      code: null,
      openid: null,
      showPositionValue: false,
      text: null,
      headimg: null,
      name: null,
      btn: [
        /* { id: 1, name: "图层", url: "tc", herf: "/rzlist", fun: "a" }, */
        { id: 2, name: "认种", url: "ry", herf: "/rzlist", fun: "b" },
        { id: 3, name: "认养", url: "rz", herf: "/rylist", fun: "b" },
        { id: 4, name: "尽责", url: "jz", herf: "/zsjz", fun: "b" },
        { id: 5, name: "排名", url: "pm", herf: "/ph", fun: "b" },
        { id: 6, name: "个人", url: "cj", herf: "/cj", fun: "b" },
        { id: 7, name: "公示", url: "gsp", herf: "/gs", fun: "b" },
        { id: 8, name: "AR", url: "AR", herf: "/rzlist", fun: "c" },
      ],
      barrage: [
        { id: 1, type: "认种", text: "张三认领了100棵芙蓉树" },
        { id: 2, type: "捐资", text: "李四捐资了60元" },
        { id: 3, type: "认种", text: "张二认领了100棵芙蓉树" },
        { id: 4, type: "认养", text: "小二认养了100棵芙蓉树" },
        { id: 5, type: "捐资", text: "王五捐资了60元" },
        { id: 6, type: "捐资", text: "赵四捐资了60元" },
      ],
      localtion:{},
      myRzry:{},
    };
  },
  methods: {
    //获取用户位置
    wxGetLocation() {
      const that = this;
      // that.$refs.map.mapGoTo(120.663, 27.994); //定位
      wx.getLocation({
        type: "wgs84", // 默认为wgs84的gps坐标，如果要返回直接给openLocation用的火星坐标，可传入'gcj02'
        success: function (res) {
          const latitude = res.latitude; // 纬度，浮点数，范围为90 ~ -90
          const longitude = res.longitude; // 经度，浮点数，范围为180 ~ -180。
          that.$refs.map.mapGoTo(longitude, latitude); //定位
        },
      });
    },
    wxAuthorization() {
      this.$axios
        .get(
          `/fapi/public/main/oauth?redirect_uri=${encodeURIComponent(
            inde_url //部署后需调整
          )}&response_url=null`,
          {}
        )
        .then((res) => {
          console.log(res.data);
          window.sessionStorage.setItem('res',res.data);//存入sessionStorage
          //this.$cookies.set("res", res.data); //存入cookise
          window.location.href = res.data;
        });
    },
    persondata(cookie, fn) {
      window.sessionStorage.setItem('code',this.$utils.getUrlKey("code"));
      // this.$cookies.set("code", this.$utils.getUrlKey("code"));
      // const code = this.$cookies.get("code");
      const that = this;
      const code = window.sessionStorage.getItem('code');
      console.log('code',code);
      this.$axios
        .get(`/fapi/public/main/invoke?code=${code}`, {})
        .then((res) => {
          if (res.data.code != 100) {
            //JSON.parse(res.data.user_info).errcode
            fn && fn(false);
          } else {
            // console.log('res',res.data);
            window.sessionStorage.setItem("token",res.data.wxscToken);
            // this.$cookies.set('token',res.data.wxscToken);
            const person = JSON.parse(res.data.userInfo); //用户全部信息
            that.headimg = person.headpic;
            that.name = person.nickname;
            console.log(person);
            console.log(JSON.parse(res.data.userInfo));
            window.sessionStorage.setItem('person',res.data.userInfo);
            //this.$cookies.set("person", person); //存入cookise
            fn && fn(true);
          }
        });
    },
    /* 矢量影像切换 */
    mapSwitch(dtlist) {
      this.$refs.map.addTileLayer(dtlist);
    },
    /* 专题叠加切换 */
    mapZt(zlist) {
      this.$refs.map.addZtLayer(zlist);
    },
    jump(data) {
      this[data.fun](data);
    },
    a(data) {
      /* 弹框 */
      this.popShow == false ? (this.popShow = true) : (this.popShow = false);
      //console.log(this.popShow);
      bus.$emit("tcEvent", this.popShow);
      //
    },
    b(data) {
      /* 跳转 */
      this.$router.push({
        path: data.herf,
        query: {
          headimg: this.headimg,
          name: this.name,
        },
      });
      console.log(this.headimg)
    },
    c() {
      this.showPositionValue = true;
      this.text = "暂不开放";
    },
    async getMyRzry(){
      const self = this;
      await self.$axios.post("/api/sys/yl/ordertree/list", {
        "nowPage": self.pageNum,
        "pageSize": 10,
        "key":""
      }, {
        headers: {
          "token":  window.sessionStorage.getItem('token')
        }
      }).then(function (res) {
        if (res.data.msg && res.data.msg.indexOf("未登录")!=-1){
          window.sessionStorage.removeItem("person");
          if (!window.sessionStorage.getItem("person")) {
            // if (!this.$cookies.get("person")) {
            let code = self.$utils.getUrlKey("code");
            self.persondata(code, (boolean) => {
              if (boolean) {
                //  code..
                console.log("成功了");
                self.$utils.wxgetsign(that,wx);//获取wx.config
              } else {
                //  验证 跳转
                self.wxAuthorization();
              }
            });
          } else {
            //  验证 跳转
            const that = this;
            console.log("已有cookie");
            const person = JSON.parse(window.sessionStorage.getItem('person'))
            self.headimg = person.headpic;
            self.name = person.nickname;
            // that.headimg = this.$cookies.get("person").headpic;
            // that.name = this.$cookies.get("person").nickname;
            self.$utils.wxgetsign(that, wx); //获取wx.config
            //this.wxAuthorization();
          }
        }
        // console.log(self.format(res.data.datas.publish_date));
        const list = res.data.datas;
        self.myRzry = list;
        console.log("我的认养",self.myRzry);
      });
    },
  },
  mounted() {
    /* 获取用户信息,通过存入用户信息判断 */
    console.log("测试进入");
    const that = this;
    if (!window.sessionStorage.getItem("person")) {
    // if (!this.$cookies.get("person")) {
      let code = this.$utils.getUrlKey("code");
      this.persondata(code, (boolean) => {
        if (boolean) {
          //  code..
          console.log("成功了");
          var that = this;
          that.$utils.wxgetsign(that, wx); //获取wx.config
        } else {
          //  验证 跳转
          this.wxAuthorization();
        }
      });

    } else {
      //  验证 跳转
      const that = this;
      console.log("已有cookie");
      const person = JSON.parse(window.sessionStorage.getItem('person'))
      that.headimg = person.headpic;
      that.name = person.nickname;
      // that.headimg = this.$cookies.get("person").headpic;
      // that.name = this.$cookies.get("person").nickname;
      that.$utils.wxgetsign(that, wx); //获取wx.config
      //this.wxAuthorization();
    }
    // that.getMyRzry()
  },

};
</script>
<style lang="less" scoped>
@import "../../assets/config/common.css";
#mapSearch {
  position: fixed;
  top: 10px;
  left: 2%;
  width: 96%;
  height: 30px;
}
.leftCj {
  position: absolute;
  left: 2%;
  top: 35%;
  background-color: #fff;
  border: 1px solid #ddd;
  border-radius: 8px;
  overflow-y: scroll;
  height: 15vh;
  .icons{
    width: 5vh;
    height: 5vh;
    padding-bottom: 1vh;
  }

  li {
    padding: 3px 7px;
    text-align: center;
    img {
      width: 20px;
    }
    p {
      margin: 0;
      border-bottom: 1px solid #ddd;
      padding-bottom: 5px;
      font-size: 10px;
    }
  }
  li:last-child p {
    border-bottom: none;
  }
}
.leftCj::-webkit-scrollbar {
  width:0px;
  height:0px;
}
#leftBtn {
  position: absolute;
  left: 2%;
  top: 20%;
  background-color: #fff;
  border: 1px solid #ddd;
  border-radius: 8px;
  li {
    padding: 3px 7px;
    text-align: center;
    img {
      width: 20px;
    }
    p {
      margin: 0;
      border-bottom: 1px solid #ddd;
      padding-bottom: 5px;
      font-size: 10px;
    }
  }
  li:last-child p {
    border-bottom: none;
  }
}
#mapBtn {
  position: absolute;
  right: 2%;
  top: 7%;
  background-color: #fff;
  border: 1px solid #ddd;
  border-radius: 8px;
  li {
    padding: 3px 7px;
    text-align: center;
    img {
      width: 20px;
    }
    p {
      margin: 0;
      border-bottom: 1px solid #ddd;
      padding-bottom: 5px;
      font-size: 10px;
    }
  }
  li:last-child p {
    border-bottom: none;
  }
}
#barrageDiv {
  position: absolute;
  width: 210px;
  bottom: 10px;
  left: 10px;
  li {
    width: 198px;
    margin: 12px 4px;
    background-color: #fff;
    border-radius: 20px;
    padding: 3px 0px;
    position: relative;
    box-shadow: 0px 2px 3px 0px rgba(0, 0, 0, 0.3);
  }
}
</style>
