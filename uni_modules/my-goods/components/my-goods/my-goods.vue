<template>
  <view class="goods-item">
    <!-- 左侧的盒子 -->
    <view class="goods-item-left">
      <radio :checked="goods.goods_state" color="#c00000" v-if="ShowRadio" @click="radioClickHandler"></radio>
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
        <uni-number-box :min="1" :value="goods.goods_count" v-if="ShowNum" @change="numChangeHandler"></uni-number-box>
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
      },
      // 是否展示图片左侧的 radio
      ShowRadio: {
        type: Boolean,
        default: false,
      },
      // 是否展示价格右侧的 NumberBox 组件
      ShowNum: {
        type: Boolean,
        defaul: false
      }
    },
    //过滤器
    filters: {
      // 把数字处理为带两位小数点的数字
      toFixed(num) {
        return Number(num).toFixed(2)
      }
    },
    methods: {
      // radio 组件的点击事件处理函数
      radioClickHandler() {
        // 通过 this.$emit() 触发外界通过 @ 绑定的 radio-change 事件，
        // 同时把商品的 Id 和 勾选状态 作为参数传递给 radio-change 事件处理函数
        this.$emit('radio-change', {
          // 商品的 Id
          goods_id: this.goods.goods_id,
          // 商品最新的勾选状态
          goods_state: !this.goods.goods_state,
        })
      },

      // NumberBox 组件的 change 事件处理函数
      numChangeHandler(val) {
        // 通过 this.$emit() 触发外界通过 @ 绑定的 num-change 事件
        this.$emit('num-change', {
          goods_id: this.goods.goods_id,
          // 商品的最新数量
          goods_count: +val
        })
      }
    }
  }
</script>

<style lang="scss">
  .goods-item {
    width: 750rpx;
    box-sizing: border-box;
    display: flex;
    padding: 10px 5px;
    border: 1px solid #f0f0f0;

    .goods-item-left {
      margin: right 5px;
      display: flex;
      justify-content: space-between;
      align-items: center;

      .goods-pic {
        width: 100px;
        height: 100px;
        display: block;
      }
    }

    .goods-item-right {
      display: flex;
      flex: 1;
      flex-direction: column;
      justify-content: space-between;

      .goods-name {
        font-size: 13px;
      }

      .goods-info-box {
        display: flex;
        justify-content: space-between;
        align-items: center;

        .goods-price {
          color: #c00000;
          font-size: 16px;
        }
      }
    }
  }
</style>