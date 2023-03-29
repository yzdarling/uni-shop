<template>
  <view>
    <view class="goods-list">
      <view v-for="(goods,i) in goodsList" :key="i" @click="gotoDetail(goods)">
        <!-- 封装 item (商品列表) 项组件 -->
        <my-goods :goods="goods"></my-goods>
      </view>
    </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        // 请求参数对象
        queryObj: {
          // 查询关键词
          query: '',
          // 商品分类Id
          cid: '',
          // 页码值
          pagenum: 1,
          // 每页显示多少条数据
          pagesize: 10,
        },
        goodsList: [],
        // 总数量
        total: 0,
        // 数据节流
        isLoading: false

      };
    },
    onLoad(option) {
      this.queryObj.query = option.query || ''
      this.queryObj.cid = option.cid || ''
      this.getGoodsList()
    },

    methods: {
      // 获取商品列表的的方法
      async getGoodsList(cb) {
        this.isLoading = true
        const {
          data: res
        } = await uni.$http.get('/api/public/v1/goods/search', this.queryObj)
        this.isLoading = false
        cb && cb()
        if (res.meta.status !== 200) return uni.showMsg()
        this.goodsList = [...this.goodsList, ...res.message.goods]
        this.total = res.message.total
      },

      //  跳转商品详情页
      gotoDetail(goods) {
        uni.navigateTo({
          url: '/subpkg/goods_detail/goods_detail?goods_id=' + goods.goods_id
        })
      }
    },

    // 上拉处理事件
    onReachBottom() {
      if (this.queryObj.pagenum * this.queryObj.pagesize >= this.total) return uni.$shpwMsg('数据加载完毕...')
      if (this.isLoading) return
      // 让页码值自增加 1
      this.queryObj.pagenum += 1
      this.getGoodsList()
    },

    // 下拉刷新事件
    onPullDownRefresh() {
      this.queryObj.pagenum = 1
      this.total = 0
      this.isloading = false
      this.goodsList = []

      this.getGoodsList(() => uni.stopPullDownRefresh())
    },


  }
</script>

<style lang="scss">

</style>