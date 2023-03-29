<template>
  <view class="goods-item">
    <!-- 左侧的盒子 -->
    <view class="goods-item-left">
      <img :src="goods.goods_small_logo || defaultPic" alt="" class="goods-pic">
    </view>
    <!-- 右侧的盒子 -->
    <view class="goods-item-right">
      <!-- 商品的名字 -->
      <view class="goods-name">
        {{goods.goods_name}}
      </view>
      <!-- 商品的价格 -->
      <view class="goods-info-box">
        <view class="goods-price">
          ￥{{goods.goods_price | toFixed}}
        </view>
      </view>
    </view>
  </view>

</template>

<script>
  export default {
    data() {
      return {
        // 默认的空图片
        defaultPic: 'https://img3.doubanio.com/f/movie/8dd0c794499fe925ae2ae89ee30cd225750457b4/pics/movie/celebrity-default-medium.png'
      }
    },
    // 定义 props 属性，用来接收外界传递到当前组件的数据
    props: {
      // 商品的信息对象
      goods: {
        type: Object,
        defaul: {}
      }
    },
    //过滤器
    filters: {
      // 把数字处理为带两位小数点的数字
      toFixed(num) {
        return Number(num).toFixed(2)
      }
    }
  }
</script>

<style lang="scss">
  .goods-item {
    display: flex;
    padding: 10px 5px;
    border: 1px solid #f0f0f0;

    .goods-item-left {
      margin: right 5px;

      .goods-pic {
        width: 100px;
        height: 100px;
        display: block;
      }
    }

    .goods-item-right {
      display: flex;
      flex-direction: column;
      justify-content: space-between;

      .goods-name {
        font-size: 13px;
      }

      .goods-info-box {
        .goods-price {
          color: #c00000;
          font-size: 16px;
        }
      }
    }
  }
</style>