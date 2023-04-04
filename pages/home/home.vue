<template>
  <view>
    <!-- 搜索组件 -->
    <view class="search-box">
      <my-search @click="gotoSearch"></my-search>
    </view>

    <!-- 轮播图区域 -->
    <swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">
      <!-- 循环渲染轮播图的 item 项 -->
      <swiper-item v-for="(item, i) in swiperList" :key="i">
        <navigator class="swiper-item" :url="'/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id">
          <!-- 动态绑定图片的 src 属性 -->
          <image :src="item.image_src"></image>
        </navigator>
      </swiper-item>
    </swiper>
    <!-- 分类导航区域 -->
    <view class="nav-list">
      <view class="nav-item" v-for="(item,index) in navList" :key="index" @click="navClickHandler(item)">
        <img :src="item.image_src" alt="" class="nav-img">
      </view>
    </view>
    <!-- 楼层区域 -->
    <view class="floor-list">
      <!-- 楼顶的item项 -->
      <view class="floor-item" v-for="(item,index) in floorList" :key="index">
        <!-- 楼层标题 -->
        <img :src="item.floor_title.image_src" alt="" class="floor-title">
        <!-- 楼顶的图片区域 -->
        <view class="floor-img-box">
          <!-- 左侧的大盒子 -->
          <navigator class="left-img-box" :url="item.product_list[0].url">
            <img :src="item.product_list[0].image_src" alt="" :style="{width:item.product_list[0].image_width + 'rpx'}"
              mode="widthFix">
          </navigator>
          <!-- 右侧的四个盒子 -->
          <view class="right-img-box">
            <navigator class="right-img-item" v-for="(item2,index2) in item.product_list" :key="index2"
              v-if="index2 !==0" :url="item2.url">
              <img :src="item2.image_src" mode="widthFix" :style="{width: item2.image_width + 'rpx'}" alt="">
            </navigator>
          </view>
        </view>
      </view>
    </view>

  </view>
</template>

<script>
  import badgeMix from "@/mixins/tabbar-badge.js"
  export default {
    mixins: [badgeMix],
    data() {
      return {
        // 轮播图的数据列表
        swiperList: [],
        // 分类导航的数据列表
        navList: [],
        // 楼层的数据
        floorList: []
      };
    },
    onLoad() {
      //  在小程序页面刚加载的时候，调用获取轮播图数据的方法
      this.getSwiperList()
      this.getNavList()
      this.getFloorList()
    },
    methods: {
      //  获取轮播图数据的方法
      async getSwiperList() {
        //  发起请求
        const {
          data: res
        } = await uni.$http.get('/api/public/v1/home/swiperdata')
        //  请求失败
        if (res.meta.status !== 200) return uni.$showMsg()
        //  请求成功，为 data 中的数据赋值
        this.swiperList = res.message
      },
      //获取分类导航数据的方法
      async getNavList() {
        const {
          data: res
        } = await uni.$http.get('/api/public/v1/home/catitems')
        //  请求失败
        if (res.meta.status !== 200) return uni.$showMsg()
        //  请求成功，为 data 中的数据赋值
        this.navList = res.message
      },
      //跳转分类页面
      navClickHandler(item) {
        if (item.name === '分类') {
          uni.switchTab({
            url: '/pages/cate/cate',
          })
        }
      },
      // 获取楼层数据的方法
      async getFloorList() {
        const {
          data: res
        } = await uni.$http.get('/api/public/v1/home/floordata')
        //  请求失败
        if (res.meta.status !== 200) return uni.$showMsg()
        // 对数据进行处理
        res.message.forEach(floor => {
          floor.product_list.forEach(prod => {
            prod.url = '/subpkg/goods_list/goods_list?' + prod.navigator_url.split('?')[1]
          })
        })
        //  请求成功，为 data 中的数据赋值
        this.floorList = res.message
      },

      //跳转搜索页面
      gotoSearch() {
        uni.navigateTo({
          url: '/subpkg/search/search'
        })
      }
    }
  }
</script>

<style lang="scss">
  swiper {
    height: 330rpx;

    .swiper-item,
    image {
      width: 100%;
      height: 100%;
    }
  }

  .nav-list {
    display: flex;
    justify-content: space-around;
    margin: 15rpx 0rpx;

    .nav-img {
      width: 128rpx;
      height: 140rpx;
    }
  }

  .floor-title {
    width: 100%;
    height: 60rpx;
    display: flex;
  }

  .floor-img-box {
    display: flex;
    padding-left: 10rxp;
  }

  .right-img-box {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-around;
  }

  .search-box {
    // 设置定位效果为“吸顶”
    position: sticky;
    top: 0;
    z-index: 999;
  }
</style>