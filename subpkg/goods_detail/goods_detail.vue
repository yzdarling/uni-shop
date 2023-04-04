<template>
  <view v-if="goods_info.goods_name" class="goods_detail-container">
    <!-- 轮播图区域 -->
    <swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">
      <swiper-item v-for="(item,i) in goods_info.pics" :key="i" @click="preview(i)">
        <img :src="item.pics_big" alt="">
      </swiper-item>
    </swiper>

    <!-- 商品信息区域 -->
    <view class="goods-info-box">
      <!-- 商品价格区 -->
      <view class="goods-info-price">
        ￥{{goods_info.goods_price}}
      </view>
      <!-- 商品信息主题区域 -->
      <view class="goods-info-body">
        <!-- 商品的名称 -->
        <view class="goods-name">
          {{goods_info.goods_name}}
        </view>
        <!-- 收藏 -->
        <view class="favi">
          <uni-icons type="star" size="18" color="gray"></uni-icons>
          <text>收藏</text>
        </view>
      </view>
      <!-- 运费 -->
      <view class="goods-ys">
        快递:免运费
      </view>
    </view>

    <rich-text :nodes="goods_info.goods_introduce"></rich-text>

    <!-- 商品导航区域 -->
    <view class="goods_nav">
      <uni-goods-nav :fill="true" :options="options" :buttonGroup="buttonGroup" @click="onClick"
        @buttonClick="buttonClick" />
    </view>

  </view>
</template>

<script>
  import {
    mapState,
    mapMutations,
    mapGetters
  } from 'vuex'

  export default {
    watch: {
      // total(newValue) {
      //   const findResult = this.options.find(x => x.text === '购物车')
      //   if (findResult) {
      //     findResult.info = newValue
      //   }
      // }

      total: {
        handler(newValue) {
          const findResult = this.options.find(x => x.text === "购物车")
          if (findResult) {
            findResult.info = newValue
          }
        },
        // immediate 属性用来声明此侦听器，是否在页面初次加载完毕后立即调用
        immediate: true
      }

    },
    data() {
      return {
        // 商品详情列表
        goods_info: {},
        // 左侧按钮组的配置对象
        options: [{
          icon: 'shop',
          text: '店铺'
        }, {
          icon: 'cart',
          text: '购物车',
          info: 0
        }],
        // 右侧按钮组的配置对象
        buttonGroup: [{
            text: '加入购物车',
            backgroundColor: '#ff0000',
            color: '#fff'
          },
          {
            text: '立即购买',
            backgroundColor: '#ffa200',
            color: '#fff'
          }
        ]
      };
    },

    computed: {
      ...mapState('m_cart', []),

      // 把 m_cart 模块中名称为 total 的 getter 映射到当前页面中使用
      ...mapGetters('m_cart', ['total']),
    },

    onLoad(option) {
      const goods_id = option.goods_id
      this.getGoodsDetail(goods_id)
    },

    methods: {
      // 
      async getGoodsDetail(goods_id) {
        const {
          data: res
        } = await uni.$http.get('/api/public/v1/goods/detail', {
          goods_id
        })
        if (res.meta.status !== 200) return uni.showMsg()

        res.message.goods_introduce = res.message.goods_introduce.replace(/<img /g, '<img style="display:block;" ')
          .replace(
            /webp/g, 'jpg')
        this.goods_info = res.message
      },

      //  实现轮播图的预览效果
      preview(i) {
        uni.previewImage({
          // 预览时，默认显示图片的索引
          current: i,
          // 所有图片 url 地址的数组
          urls: this.goods_info.pics.map(x => x.pics_big)
        })
      },

      // 店铺
      onClick(e) {
        if (e.content.text === "购物车") {
          uni.switchTab({
            url: '/pages/cart/cart'
          })
        }
      },

      // 把 m_cart 模块中的 addToCart 方法映射到当前页面使用
      ...mapMutations('m_cart', ['addToCart']),

      // 加入购物车
      buttonClick(e) {
        if (e.content.text === '加入购物车') {

          // 2. 组织一个商品的信息对象
          const goods = {
            goods_id: this.goods_info.goods_id, // 商品的Id
            goods_name: this.goods_info.goods_name, // 商品的名称
            goods_price: this.goods_info.goods_price, // 商品的价格
            goods_count: 1, // 商品的数量
            goods_small_logo: this.goods_info.goods_small_logo, // 商品的图片
            goods_state: true // 商品的勾选状态
          }

          // 3. 通过 this 调用映射过来的 addToCart 方法，把商品信息对象存储到购物车中
          this.addToCart(goods)

        }
      }
    },


  }
</script>

<style lang="scss">
  swiper {
    height: 750rpx;

    img {
      width: 100%;
      height: 100%;
    }
  }

  .goods-info-box {
    padding: 10px;
    padding-right: 0;

    .goods-info-price {
      color: #f00000;
      font-size: 18px;
      margin: 10px 0;
    }

    .goods-info-body {
      display: flex;
      justify-content: space-between;

      .goods-name {
        font-size: 13px;
        margin-right: 10px;
      }

      .favi {
        width: 120px;
        font-size: 12px;
        display: flex;
        flex-direction: column;
        align-items: center;
        border-left: 1px solid #efefef;
        color: gray;

      }
    }

    .goods-ys {
      font-size: 12px;
      color: gray;
      margin: 10px 0;
    }
  }

  .goods_nav {
    position: fixed;
    bottom: 0;
    left: 0;
    width: 100%;
  }

  .goods_detail-container {
    padding-bottom: 50px;
  }
</style>