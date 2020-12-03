<template>
  <div id="phDiv">
    <!-- 首图 -->
    <div class="bgDiv">
      <img src="../img/phbg.jpg" />
    </div>
    <!-- 内容 -->
    <div class="centent">
      <!-- 标签切换 -->
      <button-tab class="tab">
        <button-tab-item selected>
          <span>认种排行</span>
        </button-tab-item>
        <button-tab-item>
          <span>认养排行</span>
        </button-tab-item>
      </button-tab>
      <!-- 数据 -->
      <ul class="phList">
        <li v-for="i in phlist" :key="i.name">
          <p class="img" v-if=" i.id == '1'" style="background-color: #f88824;">
            <img src="../img/1.png" />
          </p>
          <p class="img" v-if=" i.id == '2'" style="background-color: #fda422;">
            <img src="../img/2.png" />
          </p>
          <p class="img" v-if=" i.id == '3'" style="background-color: #efcf46;">
            <img src="../img/3.png" />
          </p>
          <p class="img" v-else-if="i.id != '1'&&i.id != '2'&&i.id != '3'">
            <span>{{i.id}}</span>
          </p>
          <p class="person">
            <img :src="i.src" />
            <span>
              <font>捐赠人:</font>
              {{i.name}}
            </span>
          </p>
          <p class="num">
            已认领
            <span>{{i.num}}</span>棵
          </p>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import { Tab, TabItem, ButtonTab, ButtonTabItem } from "vux";
export default {
  components: {
    //Search,
    Tab,
    TabItem,
    ButtonTab,
    ButtonTabItem,
  },
  data() {
    return {
      phlist: [
        {
          id: 1,
          name: "。。。",
          src: require("@/components/img/tx/1.jpg"),
          num: "1110",
        },
        {
          id: 2,
          name: "K。",
          src: require("@/components/img/tx/2.jpg"),
          num: "1234",
        },
        {
          id: 3,
          name: "Lin",
          src: require("@/components/img/tx/3.jpg"),
          num: "124",
        },
        {
          id: 4,
          name: "yuzheng",
          src: require("@/components/img/tx/4.jpg"),
          num: "19",
        },
        {
          id: 5,
          name: "🐼",
          src: require("@/components/img/tx/5.jpg"),
          num: "14",
        },
        {
          id: 6,
          name: "黄必胜",
          src: require("@/components/img/tx/6.jpg"),
          num: "8",
        },
        {
          id: 7,
          name: "嘉",
          src: require("@/components/img/tx/7.jpg"),
          num: "7",
        },
        {
          id: 8,
          name: "今夜明珠色当随满月开",
          src: require("@/components/img/tx/8.jpg"),
          num: "6",
        },
        {
          id: 9,
          name: "上官",
          src: require("@/components/img/tx/9.jpg"),
          num: "5",
        },
        {
          id: 10,
          name: "鐿",
          src: require("@/components/img/tx/10.jpg"),
          num: "3",
        },
        {
          id: 11,
          name: "允儿最靓",
          src: require("@/components/img/tx/11.jpg"),
          num: "2",
        },
        {
          id: 12,
          name: "在寫三維自殺",
          src: require("@/components/img/tx/12.jpg"),
          num: "1",
        },
      ],
    };
  },
  methods: {},
};
</script>

<style lang="less">
@import "../../assets/config/common.css";
#phDiv /deep/ .vux-button-group > a {
  color: #04be02;
}
#phDiv /deep/ .vux-button-group > a.vux-button-group-current {
  color: #fff;
}
#phDiv {
  .bgDiv {
    width: 100%;
    height: 100px;
    overflow: hidden;
    img {
      width: 100%;
    }
  }
  .centent {
    margin-left: 15px;
    margin-right: 15px;
    margin-top: 15px;
    .phList {
      margin-top: 10px;
      li {
        width: 100%;
        height: 55px;
        margin-bottom: 15px;
        p {
          float: left;
          display: inline-block;
          margin: auto;
          height: 100%;
        }
        .img {
          width: 18%;
          background-color: #09ab7c;
          border-radius: 4px 0px 0px 4px;
          span {
            display: block;
            color: #fff;
            font-size: 24px;
            font-weight: bold;
            width: 100%;
            text-align: center;
            line-height: 50px;
          }
          img {
            width: 65%;
            padding: 11px 9px;
          }
        }
        .person {
          width: 55%;
          border: 1px solid #28ce84;
          border-left: none;
          border-right: none;
          box-sizing: border-box;
          -moz-box-sizing: border-box;
          position: relative;
          img {
            width: 35px;
            padding: 9px 5px;
            border-radius: 50%;
          }
          span {
            position: absolute;
            bottom: 18px;
            font-size: 12px;
            width: 90px;
            display: block;
            left: 50px;
            overflow: hidden;
            text-overflow: ellipsis;
            white-space: nowrap;
            font {
              color: #337c6b;
              font-size: 12px;
            }
          }
        }
        .num {
          width: 30%;
          height: 0px;
          margin-left: -11%;
          position: absolute;
          border-bottom: 60px solid #28ce84;
          border-left: 22px solid transparent;
          line-height: 60px;
          font-size: 12px;
          color: #fff;
          font-weight: bold;
          span {
            font-size: 16px;
            color: #e7fe2a;
          }
        }
      }
    }
  }
}
</style>
