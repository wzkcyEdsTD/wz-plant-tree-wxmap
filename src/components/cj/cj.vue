<template>
  <div id="cj">
    <!-- 头部背景颜色 -->
    <div class="titleDiv">
      <div class="person">
        <img :src="this.headimg" />
      </div>
      <div class="text">
        <h3>{{this.name}}</h3>
        <p>
          解锁树种：
          <span>2</span>
        </p>
      </div>
      <div>
        <x-button
          :gradients="['#fff', '#fff']"
          class="btn"
          link="/myRzry"
        >我的认种认养</x-button>
      </div>
      <div >
        <img src="../img/phone.png" class="icon" @click="Phone">
        <x-button
          :gradients="['#fff', '#fff']"
          class="btnPhone"
          @click.native="Phone"
        >绑定手机号</x-button>
      </div>
      <div class="hz">
        <div class="jb">
          <img :src="require('../img/jb.png')" />
          排名：02
        </div>
        <div class="nl">
          <img :src="require('../img/nl.png')" />
          绿色能量：100
        </div>
      </div>
    </div>
    <!-- 标题 -->
    <div class="title">
      <img :src="require('../img/xqx.png')" style="left: 10px;" />
      <h3>我的证书</h3>
      <img :src="require('../img/xqx.png')" class="imgborder" />
    </div>
    <!-- 列表 -->
    <ul class="hzlist" style="height: 78px;">
      <li v-for="i in zsList" :key="i.id" @click="lookZs(i)">
        <img :src="i.img" />
        <!-- <div class="text">芙蓉树</div> -->
      </li>
    </ul>
    <div v-transfer-dom>
      <previewer :list="IMGlist" ref="previewer"></previewer>
    </div>
    <!-- 标题 -->
    <div class="title">
      <img :src="require('../img/xqx.png')" style="left: 10px;" />
      <h3>我的解锁</h3>
      <img :src="require('../img/xqx.png')" class="imgborder" />
    </div>
    <!-- 列表 -->
    <ul class="hzlist">
      <li v-for="i in cjList" :key="i.id">
        <img :src="i.img" @click="showGYP" />
        <!-- <div class="text">芙蓉树</div> -->
      </li>
    </ul>
    <!-- 绑定手机号-->
    <phoneDialog />
    <!-- 弹框 -->
    <rzrypop></rzrypop>
  </div>
</template>

<script>
import bus from "../../assets/config/eventBus";
import { XButton, Popover, Previewer, TransferDom } from "vux";
import rzrypop from "../personal/rzryPop/rzrypop";
import PhoneDialog from "./phoneDialog";

export default {
  directives: {
    TransferDom,
  },
  components: {
    PhoneDialog,
    XButton,
    Popover,
    Previewer,
    TransferDom,
    rzrypop,
  },
  data() {
    return {
      headimg: "",
      name: "",
      timeValue: "2020",
      gypShow: true,
      IMGlist: [],
      zsList: [
        {
          id: 1,
          img: require("../img/zs.jpg"),
        },
        {
          id: 2,
          img: require("../img/zs.jpg"),
        },
        {
          id: 3,
          img: require("../img/zs.jpg"),
        },
      ],
      cjList: [
        {
          id: 1,
          name: "白玉兰",
          img: require("../img/tbyl.png"),
          unlock: true,
        },
        {
          id: 2,
          name: "合欢树",
          img: require("../img/thhs.png"),
          unlock: true,
        },
        {
          id: 3,
          name: "枇杷树",
          img: require("../img/fpps.png"),
          unlock: false,
        },
      ],
    };
  },
  created() {
    this.getRouterData();
  },
  methods: {
    lookZs(list) {
      let item = {
        src: list.img,
      };
      this.IMGlist.length = 0;
      this.IMGlist.push(item);
      this.$refs.previewer.show(0);
    },
    showGYP() {
      console.log(bus);
      bus.$emit("tcEvent", this.gypShow);
    },
    getRouterData() {
      this.headimg = this.$route.query.headimg;
      this.name = this.$route.query.name;
    },
    Phone(){
      // this.$refs.phoneDialog.phoneShow = true;
      bus.$emit("bangDingPhone");
    }
  },
};
</script>

<style lang="less" scoped>
@import "../../assets/config/common.css";
#cj {
  height: 100%;
  .titleDiv {
    width: 100%;
    /*overflow-x: hidden;*/
    height: 145px;
    background-image: url(../img/cjbg.jpg);
    background-size: 100%;
    position: relative;
    padding-top: 25px;
    div {
      width: 30%;
      display: inline-block;
      padding: 1%;
      vertical-align: middle;
      img {
        width: 65%;
        margin: auto;
        display: block;
        border-radius: 50%;
      }
      .btn {
        font-size: 12px;
        color: #28ce84 !important;
        border-radius: 20px;
        width: 100px;
        margin: auto;
        display: block;
        position: absolute;
        top: 47%;
        right: 2%;
      }
      .btnPhone{
        font-size: 12px;
        color: #28ce84 !important;
        border-radius: 20px;
        width: 102px;
        margin: auto;
        display: block;
        position: absolute;
        top: 6vh;
        left: 24vh;
        text-align: right;
      }
    }
    .text {
      color: #fff;
      position: relative;
      left: -2vh;
      p {
        span {
          font-size: 16px;
          font-weight: bold;
        }
      }
      h3 {
        font-size: 16px;
      }
    }
    .hz {
      width: 100%;
      text-align: right;
      color: #fff;
      position: absolute;
      right: 0vh;
      img {
        width: 35px;
        display: inline-block;
        position: absolute;
        top: -4px;
        left: -17px;
      }
      .jb {
        width: 80px;
        display: inline-block;
        background-color: #27ce84;
        position: relative;
        padding: 5px 10px;
        margin-right:30px;
      }
      .nl {
        width: 120px;
        display: inline-block;
        background-color: #27ce84;
        position: relative;
        padding: 5px 10px;
      }
    }
    .icon{
      width: 2.5vh;
      height: 3.5vh;
      position: absolute;
      top: 6.3vh;
      left: 25vh;
      z-index: 1;
    }
  }
  .title {
    margin-top: 15px;
    margin-left: 10px;
    margin-right: 10px;
    text-align: center;
    position: relative;
    h3 {
      margin: 0;
      font-size: 16px;
    }
    img {
      width: 30%;
      position: absolute;
      top: 5px;
    }
    .imgborder {
      right: 10px;
      transform: rotate(180deg);
      -ms-transform: rotate(180deg); /* Internet Explorer */
      -moz-transform: rotate(180deg); /* Firefox */
      -webkit-transform: rotate(180deg); /* Safari 和 Chrome */
      -o-transform: rotate(180deg); /* Opera */
    }
  }
  .hzlist {
    margin-top: 15px;
    margin-left: 15px;
    margin-right: 15px;
    li {
      width: 30%;
      float: left;
      margin-left: 2%;
      margin-top: 10px;
      img {
        width: 80%;
        margin: auto;
        display: block;
      }
    }
    .text {
      text-align: center;
      color: #fff;
      font-size: 14px;
      background-image: url(../img/bd.png);
      background-repeat: no-repeat;
      background-size: 80%;
      background-position: center;
    }
  }

}
</style>
