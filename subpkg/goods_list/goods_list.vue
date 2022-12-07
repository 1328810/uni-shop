<template>
  <view>
    <view class="goods-list">
      <view v-for="(goods,i) in goodsList" :key="i" @click="gotoDetail(goods)">
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
        queryObj:{
          // 查询关键词
          query:'',
          // 商品分类id
          cid:'',
          // 页码值
          pageNum:1,
          // 每页显示多少数据
          pageSize:10
        },
        goodsList:[],
        total:0,
        // 节流阀
        isLoading:false
      }
    },
    onLoad(options) {
      this.queryObj.query = options.query || ''
      this.queryObj.cid = options.cid || ''
      console.log(this.queryObj);
      this.getGoodsList()
    },
    methods:{
      // 获取商品列表数据的方法
      async getGoodsList(cb){
        // 打开节流阀
      this.isLoading = true
      const {data:res} =  await uni.$http.get('/api/public/v1/goods/search',this.queryObj)
      // 关闭节流阀
      this.isLoading = false
      cb && cb()
      if(res.meta.status !== 200) return uni.$showMsg()
      this.goodsList = [...this.goodsList,...res.message.goods]
      this.total = res.message.total
      },
      gotoDetail(goods) {
        uni.navigateTo({
          url:'/subpkg/goods_detail/goods_detail?goods_id=' + goods.goods_id
        })
      }
    },
    onReachBottom(){
      if(this.queryObj.pageNum * this.queryObj.pageSize >= this.total) return uni.$showMsg('数据加载完毕')
      if(this.isLoading) return
      // 让页码值+1
      this.queryObj.pageNum++
      this.getGoodsList()
    },
  onPullDownRefresh(){
    // 重置关键数据
    this.queryObj.pageNum = 1
    this.total = 0
    this.isLoading = false
    this.goodsList = []
    // 重新发起数据请求
    this.getGoodsList(() => uni.stopPullDownRefresh())
    
  }
}
</script>

<style lang="scss">

</style>
