<template>
  <div id="app">
    <div v-wechat-title="$route.name"></div>
    <!-- <router-link to="../components/rzry/index">Page1</router-link> -->
    <router-view/>
  </div>
</template>

<script>
  /* eslint-disable */
  // import Head from "./components/head.vue";
  window.onload = function () {
    document.addEventListener("touchstart", function (event) {
      if (event.touches.length > 1) {
        event.preventDefault();
      }
    });
    document.addEventListener("gesturestart", function (event) {
      event.preventDefault();
    });
  };
  export default {
    name: "App",
    data() {
      return {
        temp:1,
      };
    },
    mounted() {
      const that = this;
      /*区分关闭和刷新，关闭退出登录 start*/

      // window.onload = function() {
      //   if (!window.sessionStorage["ISlogin"]) {
      //     that.$cookies.remove("person");
      //     location.reload();
      //   } else {
      //     window.sessionStorage.removeItem("ISlogin");
      //   }
      // };
      // window.onunload = function() {
      //   window.sessionStorage["ISlogin"] = true;
      // };
      // window.onbeforeunload = function() {
      //   window.sessionStorage["ISlogin"] = true;
      // };
    }
  };
</script>

<style lang="less">

  body {
    margin: 0;
    padding: 0;
  }

  #app {
    width: 100%;
    height: 100%;
    margin: 0;
    padding: 0;
  }
</style>
