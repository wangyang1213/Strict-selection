<template>
  <div class="index">
    <!-- 头部的搜索 -->
    <div class="search">
      <div @click="toMappage">{{cityName}}</div>
      <div>
        <input type="text" placeholder="搜索商品" />
        <span class="icon"></span>
      </div>
    </div>
    <div class="swiper">
      <swiper class="swiper-container" indicator-dots="true" autoplay="true" interval="3000" circular="true" duration="500">
        <block v-for="(item, index) in banner" :key="index">
          <swiper-item class="swiper-item">
            <image class="slide-image" :src="item.image_url"/>
          </swiper-item>
        </block>
      </swiper>
    </div>
    <div class="channel">
      <div v-for="(item,index) in channel" :key="index" @click="catagroyList(item.id)">
        <img :src="item.icon_url" alt="">
        <p>{{item.name}}</p>
      </div>
    </div>
    <div class="brand">
      <div class="head">
        品牌制造商直供
      </div>
      <div class="content">
        <div v-for="(item,index) in brandList" :key="index" @click="branddetail(item.id)">
          <div>
            <p>{{item.name}}</p>
            <p class="price">{{item.floor_price}}元起</p>
          </div>
          <img :src="item.new_pic_url" alt="">
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import amapFile from "../../utils/amap-wx"
import { mapState, mapMutations } from 'vuex'
import { get } from "../../utils";
export default {
  data () {
    return {
      banner:[],
      channel:[],
      brandList:[]
    }
  },
  computed: {
    ...mapState(['cityName'])
  },
   mounted () {
    this.getData()
    this.getCityName()
  },
  methods: {
     ...mapMutations(['update']),
    toMappage () {
      let _this = this
      wx.getSetting({
        success: (res) => {
          if (!res.authSetting['scope.userLocation']){
            wx.openSetting({
              success: (res) => {
                //获取位置信息
                _this.getCityName();
              },
            })
          }else {
            wx.navigateTo({
              url: '/pages/mappage/main',
            });
            // _this.getCityName();
          }
        },
        fail: () =>{} ,
        complete: () =>{}
      })
    },
    getCityName () {
      let _this = this
      var myAmapFun = new amapFile.AMapWX({key:'256d94ac927c73a25e9177d789a1d060'});
      myAmapFun.getRegeo({
        success: function (data) {
          console.log(data)
        },
        fail: function (info) {
           _this.update({ cityName: '北京' })
        }
      })
    },

    async getData() {
      const data = await get('/index/index')
      console.log(data)
      this.banner = data.banner
      this.channel = data.channel
      this.brandList = data.brandList

    },
    catagroyList (id) {
      wx.navigateTo({
        url: '/pages/catagroylist/main?id=' +id,
      })
    },
    branddetail (id) {
      wx.navigateTo({
        url: '/pages/branddetail/main?id=' +id,
      })
    }
  }
}
</script>

<style lang="less" scoped>
@import "./style.less";
</style>