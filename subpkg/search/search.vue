<template>
  <view >
    <!-- 搜索框 -->
    <view class="search-box">
      <uni-search-bar  @input="input" :radius='100' cancelButton="none"></uni-search-bar>
    </view>
    <!-- 搜索建议列表 -->
    <view class="sugg-list" v-if="searchResults.length !==0">
      <view class="sugg-item" v-for="(item,i) in searchResults" :key="i" @click="gotoDetail(item)">
        <view class="goods-name">
          {{item.goods_name}}
        </view>
        <uni-icons type="arrowright" size="16"></uni-icons>
      </view>
    </view>
    <!-- 搜索历史 -->
    <view class="history-box" v-else>
      <!-- 标题区域 -->
      <view class="history-title">
        <text>搜索历史</text>
        <uni-icons type="trash" size="17" @click="clean()"></uni-icons>
      </view>
      <!-- 列表区域 -->
      <view class="history-list">
        <uni-tag :text="item" v-for="(item,i) in histories" :key="i" inverted="true" circle="true" @click="gotoGoodsList(item)"></uni-tag>
      </view>
    </view>
  </view>
  
</template>

<script>
  export default {
    data() {
      return {
        // 延时器
        timer:null,
        // 搜索关键词
        kw:'',
        searchResults:[],
        historyList:[]
      };
    },
    methods:{
      input(e) {
        clearTimeout(this.timer)
        this.timer = setTimeout(()=> {
          this.kw = e
          this.getSearchResults()
        },500)
      },
      async getSearchResults(){
        // 判断搜索关键词是否为空
        if(this.kw.length === 0){
          this.searchResults = []
          return
        }
        const {data:res} = await uni.$http.get('/api/public/v1/goods/search',{query:this.kw})
        if(res.meta.status !== 200) return uni.$showMsg()
        this.searchResults = res.message.goods
        this.saveSearchHistory()
      },
      gotoDetail(item){
        console.log(item);
        uni.navigateTo({
          url:'/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id
        })
      },
      saveSearchHistory(){
        const set =  new Set(this. historyList)
        console.log(set);
        set.delete(this.kw)
        set.add(this.kw)
        this.historyList =  Array.from(set)
        // 对历史数据进行持久化存储到本地
        uni.setStorageSync('kw',JSON.stringify(this.historyList))
      },
      clean(){
        this.historyList = [];
        uni.setStorageSync('kw','')
      },
      gotoGoodsList(kw){
        uni.navigateTo({
          url:'/subpkg/goods_list/goods_list?query=' + kw
        })
      }
    },
    computed:{
      histories(){
        return [...this.historyList].reverse()
      }
    },
    onLoad() {
      this.historyList = JSON.parse(uni.getStorageSync('kw') || '[]')
    }
  }
</script>

<style lang="scss">
.search-box {
  position: sticky;
  top:0;
  z-index: 999;
}
.sugg-list {
  padding: 0 5px;
  .sugg-item {
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-size: 12px;
    padding: 13px 0;
    border-bottom: 1px solid #efefef;
    .goods-name{
      white-space: nowrap;
      overflow: hidden;
      text-overflow: ellipsis;
    }
  }
}
.history-box {
  padding: 0 5px;
  .history-title {
    display: flex;
    justify-content: space-between;
    height: 40px;
    align-items: center;
    font-size: 13px;
    border: 1px solid #efefef;
  }
  .history-list {
    display: flex;
    flex-wrap: nowrap;
    .uni-tag {
      margin-top: 5px;
      margin-right: 5px;
    }
  }
}
</style>
