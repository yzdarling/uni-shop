<template>
  <view class="cart-container" v-if="cart.length !== 0">
    <!-- 收货地址组件 -->
    <my-address></my-address>

    <!-- 购物车商品列表的标题区域 -->
    <view class="cart-title">
      <uni-icons type="shop" size="18"></uni-icons>
      <text class="cart-title-text">购物车</text>
    </view>

    <!-- 商品列表区域 -->
    <!-- uni-swipe-action 是最外层包裹性质的容器 -->
    <uni-swipe-action>
      <block v-for="(goods,i) in cart" :key="i">
        <uni-swipe-action-item :right-options="options" @click="swipeActionClickHandler(goods)">
          <my-goods :goods="goods" :ShowRadio="true" :ShowNum="true" @radio-change="radioChangeHandler"
            @num-change="numberChangeHandler"></my-goods>
        </uni-swipe-action-item>
      </block>
    </uni-swipe-action>

    <!-- 结算组件 -->
    <my-settle></my-settle>
  </view>

  <!-- 空白购物车的区域 -->
  <view class="empty-cart" v-else>
    <img src="/static/cart_empty@2x.png" alt="" class="empty-img">
    <text class="tip-text">空空如也</text>
  </view>
</template>

<script>
  import badgeMix from "@/mixins/tabbar-badge.js"
  import {
    mapState,
    mapMutations
  } from 'vuex'
  export default {
    mixins: [badgeMix],
    data() {
      return {
        options: [{
          text: '删除',
          style: {
            backgroundColor: '#c00000'
          }
        }]
      };
    },
    computed: {
      ...mapState('m_cart', ['cart'])
    },
    methods: {
      ...mapMutations('m_cart', ['updateGoodsState', 'updateGoodsCount', 'removeGoodsById']),
      // 商品选中状态变化
      radioChangeHandler(e) {
        this.updateGoodsState(e)
      },
      //  商品的数量发生了变化
      numberChangeHandler(e) {
        this.updateGoodsCount(e)
      },

      // 滑动删除商品
      swipeActionClickHandler(goods) {
        this.removeGoodsById(goods.goods_id)
      }
    }
  }
</script>

<style lang="scss">
  .cart-title {
    height: 40px;
    display: flex;
    align-items: center;
    margin-left: 5px;
    border-bottom: 1px solid #efefef;

    .cart-title-text {
      font-size: 14px;
      margin-left: 10px;
    }
  }

  .cart-container {
    padding-bottom: 50px;
  }

  .empty-cart {
    display: flex;
    flex-direction: column;
    align-items: center;
    padding-top: 150px;

    .empty-img {
      width: 90px;
      height: 90px;
    }

    .tip-text {
      font-size: 12px;
      color: gray;
      margin-top: 15px;
    }
  }
</style>