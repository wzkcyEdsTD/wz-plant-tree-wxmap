<template>
  <div id="detailsRecord" style="padding-bottom: 9vh;">
    <!-- 图片 -->
    <div class="imgDiv">
      <img :src="require('../img/rs1.jpg')" />
      <!-- 标题 -->
      <h3>榕树</h3>
    </div>
    <!-- 标题 -->
    <div class="title">
      <img :src="require('../img/xqx.png')" style="left: 10px;" />
      <h3>认养信息</h3>
      <img :src="require('../img/xqx.png')" class="imgborder" />
    </div>
    <!-- 养护信息 -->
    <div class="tableDiv">
      <x-table :full-bordered="false" :content-bordered="false" :cell-bordered="false">
        <tbody>
          <tr>
            <th>认养人：</th>
            <td>温州设计集团有限公司</td>
          </tr>
          <tr>
            <th>认养区域：</th>
            <td>鹿城区学院中路片区</td>
          </tr>
          <tr>
            <th>认养树种:</th>
            <td>榕树</td>
          </tr>
          <tr>
            <th>认养时间：</th>
            <td>2020年08月08日</td>
          </tr>
          <tr>
            <th>养护机构：</th>
            <td>温州园林养护公司</td>
          </tr>
          <tr>
            <th>树木位置：</th>
            <td>杨府山公园</td>
          </tr>
        </tbody>
      </x-table>
    </div>
    <!-- 标题 -->
    <div class="title">
      <img :src="require('../img/xqx.png')" style="left: 10px;" />
      <h3>养护信息</h3>
      <img :src="require('../img/xqx.png')" class="imgborder" />
    </div>
    <!-- 养护记录 -->
    <group v-for="(list,index) in yhjlList" :key="index">
      <cell
        :title="'第'+list.id+'次养护'"
        :value="list.time"
        is-link
        :arrow-direction="list.showContent ? 'up' : 'down'"
        @click.native="list.showContent = !list.showContent"
        style="color: #009470;font-size:16px"
      ></cell>
      <div class="slide" :class="list.showContent?'animate':''">
        <p>{{list.text}}</p>
        <scroller lock-y scrollbar-x style="margin-bottom: 15px;">
          <div class="img">
            <div class="img-item" v-for="imglist in list.img" :key="imglist.id">
              <img :src="imglist.src" @click="previewImage" />
              <div v-transfer-dom>
                <previewer :list="IMGlist" ref="previewer"></previewer>
              </div>
            </div>
          </div>
        </scroller>
      </div>
    </group>
    <div class="nextBtn">
      <x-button class="btn" :gradients="['#fda422', '#fda422']" @click.native="upload">上传养护记录</x-button>
      <x-button class="btn" :gradients="['#09ab7c', '#09ab7c']" @click.native="zhongShu">去认种认养</x-button>
    </div>
    <choice-identity ref="choiceIdentity"></choice-identity>
  </div>
</template>

<script>
import {
  XTable,
  Scroller,
  Cell,
  CellBox,
  CellFormPreview,
  Group,
  Previewer,
  XButton,
  TransferDom,
} from "vux";
import {inde_url, url} from "../../assets/config/config";
import wx from "weixin-js-sdk";
import ChoiceIdentity from "./choiceIdentity";
export default {
  directives: {
    TransferDom,
  },
  components: {
    ChoiceIdentity,
    XButton,
    XTable,
    Scroller,
    Cell,
    CellBox,
    CellFormPreview,
    Group,
    Previewer,
  },
  data() {
    return {
      //暂时存储图片
      IMGlist: [],
      yhjlList: [
        {
          id: 3,
          showContent: true,
          time: "2020-08-11",
          text:
            "对公园内认种认养植物进行浇水、施肥。",
          img: [
            { id: 1, src: require("@/components/img/10.jpg") },
            { id: 2, src: require("@/components/img/11.jpg") },
            { id: 3, src: require("@/components/img/12.png") },
          ],
        },
        {
          id: 2,
          showContent: true,
          time: "2020-07-12",
          text:
            "对公园内认种认养植物进行剪枝。",
          img: [
            { id: 1, src: require("@/components/img/4.jpg") },
            { id: 2, src: require("@/components/img/5.jpg") },
            { id: 3, src: require("@/components/img/6.jpg") },
          ],
        },
        {
          id: 1,
          showContent: true,
          time: "2020-06-18",
          text:
            "对公园内认种认养植物进行除虫、施肥。",
          img: [
            { id: 1, src: require("@/components/img/7.jpg") },
            { id: 2, src: require("@/components/img/8.jpg") },
            { id: 3, src: require("@/components/img/9.jpg") },
          ],
        },
      ],
      headimg:undefined,
      name:undefined,
      orderCount:undefined,
      roleCount:undefined,
      identity:undefined,
    };
  },
  methods: {
    previewImage(e) {
      if (e.target.nodeName == "IMG") {
        let url = e.target.currentSrc;
        let item = {
          src: url,
        };
        this.IMGlist.length = 0;
        this.IMGlist.push(item);
        this.$refs.previewer[0].show(0);
      } else {
        return;
      }
    },
    upload(){
      /*
      * rolecount > 0 代表他是业主单位,
      * ordercount > 0 代表他认种了
      * */
      if (this.roleCount>0 && this.orderCount>0){
        this.$refs.choiceIdentity.identityShow = true
        if (!!this.identity){
          this.$router.push('/yhjl');
        }else {
          this.$vux.toast.text("请先选择要上传记录的身份")
        }
      }else {
        if (this.roleCount>0 && this.orderCount ==0){
          //业主单位
          this.identity = "1";
        }else if (this.orderCount>0 && this.roleCount==0){
          //认种人
          this.identity = "2";
        }
        if (!!this.identity){
          this.$router.push('/yhjl');
        }
      }



    },
    zhongShu(){
      console.log("认种认养")
      // debugger
      this.$router.push('/map');
    },
    wxAuthorization() {
      this.$axios
        .get(
          `/fapi/public/main/oauth?redirect_uri=${encodeURIComponent(
            `${url}/#/detailsRecord` //部署后需调整
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
      const code = window.sessionStorage.getItem('code');
      console.log('code',code);
      const that = this;
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
    async getYHJL(){
      const self = this;
      await self.$axios.post("/api/sys/yl/userrole/getUserRoleMobile", {}, {
        headers: {
          "token":  window.sessionStorage.getItem('token')
        }
      }).then(function (res) {
        if (res.data.msg.indexOf("成功")!=-1){
          if (res.data.code == 100){
            self.orderCount = res.data.datas.orderCount;
            self.roleCount = res.data.datas.roleCount;
            console.log(res.data.datas);
            console.log(self.orderCount>0?`已认种`:'未认种')
            console.log(self.roleCount>0?`是业主单位`:'不是业主单位')
          }
        }else{
          window.sessionStorage.removeItem("person");
          if (!window.sessionStorage.getItem("person")) {
            // if (!this.$cookies.get("person")) {
            let code = this.$utils.getUrlKey("code");
            this.persondata(code, (boolean) => {
              if (boolean) {
                //  code..
                console.log("成功了");

                that.$utils.wxgetsign(that, wx); //获取wx.config
              } else {
                //  验证 跳转
                that.wxAuthorization();
              }
            });
          } else {
            //  验证 跳转
            console.log("已有cookie");
            const person = JSON.parse(window.sessionStorage.getItem('person'))
            that.headimg = person.headpic;
            that.name = person.nickname;
            // that.headimg = this.$cookies.get("person").headpic;
            // that.name = this.$cookies.get("person").nickname;
            that.$utils.wxgetsign(that, wx); //获取wx.config
            //this.wxAuthorization();
          }
        }
      });
      // await self.$axios.post("/api/sys/yl/maintain/list", {
      //   "pageSize":10,
      //   "nowPage":1,
      //   "key":"",
      //   "bean":{"project_num":"7"}
      //   }, {
      //   headers: {
      //     "token":  window.sessionStorage.getItem('token')
      //   }
      // }).then(function (res) {
      //   console.log(res);
      //   if (res.data.msg.indexOf("成功")!=-1){
      //     if (res.data.code == 100){
      //
      //     }
      //   }else{
      //     window.sessionStorage.removeItem("person");
      //     if (!window.sessionStorage.getItem("person")) {
      //       // if (!this.$cookies.get("person")) {
      //       let code = this.$utils.getUrlKey("code");
      //       this.persondata(code, (boolean) => {
      //         if (boolean) {
      //           //  code..
      //           console.log("成功了");
      //
      //           that.$utils.wxgetsign(that, wx); //获取wx.config
      //         } else {
      //           //  验证 跳转
      //           that.wxAuthorization();
      //         }
      //       });
      //     } else {
      //       //  验证 跳转
      //       console.log("已有cookie");
      //       const person = JSON.parse(window.sessionStorage.getItem('person'))
      //       that.headimg = person.headpic;
      //       that.name = person.nickname;
      //       // that.headimg = this.$cookies.get("person").headpic;
      //       // that.name = this.$cookies.get("person").nickname;
      //       that.$utils.wxgetsign(that, wx); //获取wx.config
      //       //this.wxAuthorization();
      //     }
      //   }
      // });
    }
  },
  mounted() {
    ////"https://open.weixin.qq.com/connect/oauth2/authorize?appid=wx10e914f13eeac627&redirect_uri=http%3A%2F%2Frzry.go577.net%2F%23%2Fmap&response_type=code&scope=snsapi_userinfo&state=null#wechat_redirect"
    /* 获取用户信息,通过存入用户信息判断 */
    console.log("测试进入");
    const that = this;
    that.getYHJL();
  }
};
</script>

<style lang="less" scoped>
@import "../../assets/config/common.css";
#detailsRecord /deep/ .weui-cell__ft {
  color: #009470;
  font-size: 16px;
}
#detailsRecord /deep/ .weui-cell_access .weui-cell__ft:after {
  color: #009470;
  border-color: #009470;
}
#detailsRecord /deep/ .weui-cells:after,
#detailsRecord /deep/ .weui-cells:before {
  border: none;
}
#detailsRecord {
  .imgDiv {
    width: 100%;
    height: 150px;
    overflow: hidden;
    position: relative;
    img {
      width: 100%;
      margin: 0;
    }
    h3 {
      margin: 0;
      position: absolute;
      bottom: 0;
      left: 0;
      color: #fff;
      padding: 5px 15px;
      font-size: 14px;
      background-color: #28ce82;
      border-radius: 0 16px 16px 0;
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
  .tableDiv {
    margin-left: 15px;
    margin-right: 15px;
    font-size: 14px;
    th {
      width: 50%;
      text-align: right;
      color: #000;
    }
    td {
      width: 50%;
      text-align: left;
      color: #289b7a;
    }
  }
  .img {
    height: 100px;
    position: relative;
    width: 650px;
  }
  .img-item {
    width: 200px;
    height: 100px;
    background-color: #ccc;
    display: inline-block;
    margin-left: 15px;
    float: left;
    text-align: center;
    line-height: 100px;
    img {
      width: 100%;
    }
  }
  .slide {
    padding: 0px 11px 0px 10px;
    margin: 0px 15px 0px 15px;
    border-radius: 8px;
    overflow: hidden;
    max-height: 0;
    transition: max-height 0.5s cubic-bezier(0, 1, 0, 1) -0.1s;
    background-color: #f5f5f5;
  }
  .animate {
    max-height: 9999px;
    transition-timing-function: cubic-bezier(0.5, 0, 1, 0);
    transition-delay: 0s;
  }
}

.nextBtn {
  width: 100%;
  position: fixed;
  bottom: 0;
  left: 0;
  background-color: #fff;
  border-top: 1px solid #ddd;
  z-index: 10;
  .btn {
    width: 17vh;
    float: right;
    margin-right: 10px;
    margin-bottom: 10px;
    margin-top: 10px;
    height: 5vh;
    font-size: 15px;
  }
}
</style>
