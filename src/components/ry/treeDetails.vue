<template>
  <div id="treeDetailsDiv">
    <!-- 图片 -->
    <div class="imgDiv">
      <img :src="require('../img/rs.jpg')" />
      <!-- 标题 -->
      <h3>剩余：200棵</h3>
    </div>
    <!-- 标题 -->
    <div class="title">
      <img :src="require('../img/xqx.png')" style="left: 10px;" />
      <h3>榕树</h3>
      <img :src="require('../img/xqx.png')" class="imgborder" />
    </div>
    <!-- 列表 -->
    <grid :cols="1" :show-vertical-dividers="false" :show-lr-borders="false">
      <grid-item v-for="i in list" :key="i.name">
        <div class="gridList">
          <!-- 图片 -->
          <div class="imgDiv">
            <!-- 序号 -->
            <div class="xhnum">
              <span>{{i.id}}</span>
            </div>
            <img :src="i.img" />
          </div>
          <!-- 右侧数据 -->
          <div class="data">
            <p>
              <span>编号：{{i.num}}</span>
              <span style="float: right;">认养年限：{{i.time}}年</span>
            </p>
            <p>树木位置：{{i.park}}</p>
            <div class="money">
              <p>￥{{i.money}}/株</p>
              <x-button :gradients="['#fda422', '#fda422']" class="btn" link="/ryapplyInput">立即认养</x-button>
            </div>
          </div>
        </div>
      </grid-item>
    </grid>
  </div>
</template>

<script>
import { Grid, GridItem, XButton } from "vux";
export default {
  components: {
    Grid,
    GridItem,
    XButton,
  },
  data() {
    return {
      list: [
        {
          id: 1,
          name: "榕树1",
          img: require("@/components/img/rs1.jpg"),
          num: "0000001",
          park: "白鹿洲公园",
          time: 2,
          money: 200,
        },
        {
          id: 2,
          name: "榕树2",
          img: require("@/components/img/rs2.jpg"),
          num: "0000002",
          park: "白鹿洲公园",
          time: 2,
          money: 200,
        },
        {
          id: 3,
          name: "榕树3",
          img: require("@/components/img/rs3.jpg"),
          num: "0000003",
          park: "白鹿洲公园",
          time: 2,
          money: 200,
        },
        {
          id: 4,
          name: "榕树4",
          img: require("@/components/img/rs4.jpg"),
          num: "0000004",
          park: "白鹿洲公园",
          time: 2,
          money: 200,
        },
        {
          id: 5,
          name: "榕树5",
          img: require("@/components/img/rs5.jpg"),
          num: "0000005",
          park: "白鹿洲公园",
          time: 2,
          money: 200,
        },
        {
          id: 6,
          name: "榕树6",
          img: require("@/components/img/rs6.jpg"),
          num: "0000006",
          park: "白鹿洲公园",
          time: 2,
          money: 200,
        },
      ],
    };
  },
  methods:{
    async getToken(){
      const self = this;
      await this.$axios.get('/fapi/public/main/doLogintest?id=o3ttg1rWgdtcWyI2zHqKzXekf8zU&openid=o3ttg1rWgdtcWyI2zHqKzXekf8zU')
        .then(res=>{
          window.sessionStorage.setItem("token",res.data.wxscToken)
          // self.$cookies.set('token',res.data.wxscToken);
        });
    },
    async getData(){
      const self = this;
      console.log('当前页数',self.pageNum);
      if(!!!window.sessionStorage.getItem('token')){
        await self.getToken();
      }
      if (!!key){
        self.key = key;
      }else{
        self.key = '';
      }
      console.log('key',self.key);
      await self.$axios.post("/api/sys/yl/tree/list", {
        "nowPage": self.pageNum,
        "pageSize": 10,
        "key":""
      }, {
        headers: {
          "token": window.sessionStorage.getItem('token')
        }
      }).then(function (res) {
        // debugger
        if (res.data.code == -1){
          // -1 未登录 token出错
          self.getToken();
          self.getData();
        }else{
          console.log('listData', res.data.datas);
        }

      });

    },
  },
  mounted() {
    const that = this;
    that.getData();
  }
};
</script>

<style lang="less" scoped>
@import "../../assets/config/common.css";
#treeDetailsDiv /deep/ .weui-grids:before {
  border-top: none;
}
#treeDetailsDiv /deep/ .weui-grid {
  padding: 10px 10px;
}
#treeDetailsDiv /deep/ .weui-grid:after {
  border-bottom: none;
}
#treeDetailsDiv /deep/ a:-webkit-any-link {
  text-decoration: none;
}
#treeDetailsDiv /deep/ .weui-progress__bar {
  height: 10px;
  border-radius: 10px;
  background-color: #15ae6b;
}
#treeDetailsDiv /deep/ .weui-progress__inner-bar {
  border-radius: 10px;
  background-color: #fafafa;
}
#treeDetailsDiv {
  width: 100%;
  .imgDiv {
    width: 100%;
    height: 150px;
    overflow: hidden;
    position: relative;
    img {
      width: 100%;
    }
    h3 {
      margin: 0;
      position: absolute;
      bottom: 0;
      left: 0;
      color: #fff;
      padding: 5px 15px;
      font-size: 14px;
      background-color: #fda422;
      border-radius: 0 16px 16px 0;
    }
  }
  .title {
    margin-left: 10px;
    margin-right: 10px;
    margin-top: 15px;
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
  .gridList {
    width: 100%;
    height: 100px;
    border: 1px solid #28ce84;
    background-color: #fff;
    position: relative;
    .imgDiv {
      width: 25%;
      height: 100px;
      overflow: hidden;
      position: relative;
      display: inline-block;
      img {
        width: 100px;
      }
      .xhnum {
        position: absolute;
        width: 0px;
        height: 0;
        border: 17px solid transparent;
        border-top-color: #28ce84;
        border-left-color: #28ce84;
        span {
          position: absolute;
          top: -15px;
          left: -12px;
          color: #fff;
          font-size: 14px;
        }
      }
    }
    p {
      margin: 0;
      color: #000;
      font-size: 14px;
      padding: 7px 6px;
    }
    .data {
      width: 75%;
      display: inline-block;
      float: right;
      position: relative;
      height: 100%;
    }
    .money {
      background-color: #28ce84;
      position: absolute;
      width: 100%;
      bottom: 0;
    }
    .money p {
      color: #fff;
      font-weight: bold;
      font-size: 16px;
      display: inline-block;
    }
    .btn {
      float: right;
      width: 70px;
      height: 26px;
      margin: 4px 2px;
      padding: 0;
      font-size: 12px;
      line-height: 28px;
      border-radius: 12px;
    }
  }
}
</style>
