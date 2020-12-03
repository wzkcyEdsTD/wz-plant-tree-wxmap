<template>
  <!-- 显示框 -->
  <div v-transfer-dom>
    <popup v-model="show">
      <div class="textDiv">
        <h3>
          {{xmmc}}
          <p class="vr" @click="VRjump()">
            <img src="../../img/XVR.png" />
            全景
          </p>
        </h3>
        <x-button v-show="!projectShow" :gradients="['#28CE84', '#28CE84']" class="btn" link="/detailsRecord">查看详情</x-button>
        <x-button v-show="projectShow" :gradients="['#fda422', '#fda422']" class="btn" @click.native="xiangQing(projectData)">立即认种</x-button>
        <p style="float: left;">详细地址：{{xxdz}}</p>
        <div style="padding: 15px 0;clear: both;">
          <x-progress :percent="(snum / znum) * 100" :show-cancel="false" style="width:100%"></x-progress>
          <p style="text-align: center;">
            已认种：
            <span style="color:#28ce84">{{snum||0}}/{{znum}}</span>
            棵
          </p>
        </div>
      </div>
    </popup>
  </div>
</template>

<script>
/* eslint-disable */
import bus from "../../../assets/config/eventBus";
import { TransferDom, Popup, XProgress, XButton, trim } from "vux";
export default {
  directives: {
    TransferDom,
  },
  components: {
    Popup,
    XProgress,
    XButton,
    trim,
  },
  data() {
    return {
      show: false,
      projectShow:false,
      percent: undefined,
      xxdz:undefined,
      ssgy: undefined,
      dkbh: undefined,
      znum: undefined,
      snum: undefined,
      dklx: undefined,
      smlx:undefined,
      xmmc:undefined,
      VR: undefined,
      list:undefined,
      projectData:undefined,
    };
  },
  methods: {
    /* vr跳转 */
    VRjump() {
      window.location.href = this.VR;
    },

    async getToken(){
      const self = this;
      await this.$axios.get('/fapi/public/main/doLogintest?id=o3ttg1rWgdtcWyI2zHqKzXekf8zU&openid=o3ttg1rWgdtcWyI2zHqKzXekf8zU')
        .then(res=>{
          window.sessionStorage.setItem('token',res.data.wxscToken);
        });
    },
    async getData(key,fn){
      const self = this;
      if (!!key){
        self.key = key;
      }else{
        self.key = '';
      }
      await self.$axios.post("/api/sys/yl/project/list", {
        "nowPage": self.pageNum,
        "pageSize": 10,
        "key":self.key
      }, {
        headers: {
          "token":  window.sessionStorage.getItem('token')
        }
      }).then(function (res) {
        if (res.data.code == -1){
          self.getToken();
          self.getData()
        }else{
          self.list = res.data.datas;
          self.projectData = res.data.datas[0];
          if (self.projectData && self.projectData.pubstate =="1"){
            self.projectShow = true;
          }
        }
      });

    },

    async getProjectDetail(data){
      if (data) {
        const that = this;
        await that.getData(data.NAME);
        if (that.list && that.list.length){
          // debugger
          that.show = true;
          const item = that.list[0]
          that.ssgy = data.NAME; //所属公园
          that.xmmc = item.project_name;
          that.smlx = item.tree_name; //树木类型
          that.xxdz = data.ADDRESS;
          that.znum = item.project_scale; //总树木
          that.snum = item.yrzs; //剩余树木
          that.VR = item.ar_url; //vr
          // that.VR = "https://720yun.com/t/aabjvpuktv6";
          // that.percent = (trim(that.snum) / trim(that.znum)) * 100; //进度条
        }else {
          that.$vux.toast.text("所选区域暂无发布的项目")
        }
        // that.show = true;

        //console.log(date);
      }
    },

    xiangQing(project){
      this.$router.push({path:"/applyInput",query:{projectItem:project}}); //跳转树详情
    }
  },
  mounted() {
    /* vr跳转 */
    var that = this;
    bus.$on("userDefinedEvent", function (data) {
        that.getProjectDetail(data);
    });
  },
};
</script>
<style lang="less" scoped>
.textDiv /deep/ .weui-progress__bar {
  height: 10px;
  background-color: #e1e1e1;
  border-radius: 8px;
}
.textDiv /deep/ .weui-progress__inner-bar {
  border-radius: 8px;
}
.textDiv {
  padding: 5px 10px;
  .vr {
    position: absolute;
    height: 17px;
    top: -5px;
    right: 92px;
    margin: auto;
    z-index: 10;
    font-weight: normal;
    font-size: 12px;
    border: 1px solid #ddd;
    border-radius: 6px;
    padding: 5px 5px;
    background-color: #fff;
    img {
      width: 17px;
      vertical-align: middle;
    }
  }
  .btn {
    width: 81px;
    position: absolute;
    top: 10px;
    right: 10px;
    height: 28px;
    font-size: 12px;
  }
  h3 {
    margin: 0;
    margin-top: 10px;
    position: relative;
  }
  p {
    margin: 0;
    margin-top: 10px;
    color: #666;
  }
}
</style>
