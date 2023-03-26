<template>
  <view>
    <view class="scroll-view-container">
      <!-- 左侧的滚动视图区域 -->
      <scroll-view class="left-scroll-view" :scroll-y="true" :style="{height: wh + 'px'}">
        <block v-for="(item,index) in cateList" :key="index">
          <view :class="['left-scroll-view-item',index === active? 'active' : '']" @click="activeChange(index)">
            {{item.cat_name}}
          </view>
        </block>
      </scroll-view>
      <!-- 右侧的滚动视图区域 -->
      <scroll-view :scroll-y="true" :style="{height: wh + 'px'}" :scroll-top="scrollTop">
        <!-- 二级分类标题 -->
        <view class="cate-lv2" v-for="(item2,index2) in cateLevel2" :key="index2">
          <view class="cate-lv2-item">/{{item2.cat_name}}/</view>
          <!-- 二姐分类下的 三级分类列表 -->
          <view class="cate-lv3-list">
            <!-- 三级分类的item项 -->
            <view class="cate-lv3-item" v-for="(item3,index3) in item2.children" :key="index3"
              @click="gotoGoodsList(item3)">
              <!-- 图片 -->
              <img :src="item3.cat_icon.replace('dev','web')" alt="">
              <!-- 文字 -->
              <text>{{item3.cat_name}}</text>
            </view>
          </view>
        </view>

      </scroll-view>
    </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        // 当前设备可用的高度
        wh: 0,
        // 分类数据列表
        cateList: [],
        // 当前选中项的索引，默认让第一项被选中
        active: 0,
        //二级分类列表
        cateLevel2: [],
        // 滚动条距离顶部的距离
        scrollTop: 0
      };
    },
    onLoad() {
      // 获取当前系统的信息
      const sysInfo = uni.getSystemInfoSync()
      // 为 wh 窗口可用高度动态赋值
      this.wh = sysInfo.windowHeight

      this.getCateList()
    },

    methods: {
      // 获取分类列表的数据
      async getCateList() {
        const {
          data: res
        } = await uni.$http.get('/api/public/v1/categories')
        if (res.meta.status !== 200) return $showMsg()
        this.cateList = res.message

        //为二级分类赋值
        this.cateLevel2 = res.message[0].children
      },

      activeChange(index) {
        this.active = index

        //重新为二级分类赋值
        this.cateLevel2 = this.cateList[index].children

        // 让 scrollTop 的值在 0 与 1 之间切换
        this.scrollTop = this.scrollTop === 0 ? 1 : 0
      },

      // 跳转到商品列表页面
      gotoGoodsList(item) {
        uni.navigateTo({
          url: '/subpkg/goods_list/goods_list?cid=' + item.cat_id

        })
      }
    }
  }
</script>

<style lang="scss">
  .scroll-view-container {
    display: flex;

    .left-scroll-view {
      width: 120px;

      .left-scroll-view-item {
        line-height: 60px;
        background-color: #f7f7f7;
        text-align: center;
        font-size: 12px;

        // 激活项的样式
        &.active {
          background-color: #ffffff;
          position: relative;

          // 渲染激活项左侧的红色指示边线
          &::before {
            content: ' ';
            display: block;
            width: 3px;
            height: 40px;
            background-color: #c00000;
            position: absolute;
            left: 0;
            top: 50%;
            transform: translateY(-50%);
          }
        }
      }
    }
  }

  .cate-lv2-item {
    font-size: 12px;
    font-weight: bold;
    text-align: center;
    padding: 15px 0;
  }

  .cate-lv3-list {
    display: flex;
    flex-wrap: wrap;

    .cate-lv3-item {
      width: 33.33%;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      margin-bottom: 10px;

      img {
        width: 60px;
        height: 60px;
      }

      text {
        font-size: 12px
      }
    }
  }
</style>