<template>
  <div id="app">
    <!-- 头部 -->
   <app-header :poiInfo="poiInfo"></app-header>
   <!-- 导航 -->
   <app-nav :commentNum= 'commentNum'></app-nav>
   <!-- 内容 -->
   <keep-alive>
    <router-view></router-view>
   </keep-alive>
  </div>
</template>

<script>
import Header from "./components/header/Header";
import Nav from "./components/nav/Nav";
export default {
  name: "App",
  components: {
    "app-header": Header,
    "app-nav": Nav
  },
  data() {
    return {
      poiInfo: {},
      commentNum: 0
    };
  },
  created() {
    // fetch("/api/goods")
    //   .then(res =>{
    //     return res.json()
    //   })
    //   .then(response =>{
    //     // console.log(response)
    //     if(response.code == 0){
    //       this.poiInfo = response.data.poi_info
    //       // console.log(this.poiInfo)
    //     }
    //   })

    // fetch("/api/ratings")
    //   .then(res => {
    //     return res.json();
    //   })
    //   .then(response => {
    //     console.log(response.data);
    //     this.commentNum = response.data.comment_num;
    //   });

    fetch("https://wd2151358536yxwfcu.wilddogio.com/data.json")
      .then(res => {
        return res.json();
      })
      .then(data => {
        // console.log(data)
        this.poiInfo = data.poi_info;
      });

    fetch("https://wd6572388885nxklda.wilddogio.com/data.json")
      .then(res => {
        return res.json();
      })
      .then(response => {
        // console.log(response);
        this.commentNum = response.comment_num;
      });
  }
};
</script>

<style>
</style>
