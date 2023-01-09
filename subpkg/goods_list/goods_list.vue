<template>
  <view>
      <block class="goods-list">
          <view v-for="(goods, i) in goodsList" :key="i" @click="gotoDetail(goods)">
            <my-goods :goods="goods"></my-goods>
          </view>
        </block>
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
          pagesize: 10
        },
        goodsList:[],
        total: 0,
        isloading:false
        };
    },
    onLoad(options) {
      // 将页面参数转存到 this.queryObj 对象
      this.queryObj.query = options.query || ''
      this.queryObj.cid = options.cid || ''
      this.getGoodsList()
    },
    methods: {
      // 获取商品列表数据的方法
      async getGoodsList(cb) {
        this.isloading = true
        // 发起请求
        const { data: res } = await uni.$http.get('/api/public/v1/goods/search', this.queryObj)
        this.isloading = false
        cb && cb()
        if (res.meta.status !== 200) return uni.$showMsg()
        // 为数据赋值
        this.goodsList = [...this.goodsList, ...res.message.goods]
        this.total = res.message.total
      },
      gotoDetail(goods){
        uni.navigateTo({
            url: '/subpkg/goods_detail/goods_detail?goods_id=' + goods.goods_id
        })
      }
    },
    onReachBottom() {
      if(this.queryObj.pagenum * this.queryObj.pagesize >= this.total) return uni.$showMsg()
      if(this.isloading) return
      this.queryObj.pagenum++
      this.getGoodsList()
    },
    onPullDownRefresh(){
      this.queryObj.pagenum = 1
      this.total = 0
      this.isloading = false
      this.goodsList = []
      
      this.getGoodsList(() => uni.stopPullDownRefresh())
    }
  }
</script>

<style lang="scss">

</style>