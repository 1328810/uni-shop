<template>
  <view class="my-search-container"  :style="{'background-color':bgcolor}" @click="searchBoxHandler">
    <!-- 使用view组件模拟input输入框 -->
    <view class="my-search-box" :style="{'border-radius':radius + 'px'}">
      <uni-icons type="search" size="17px"></uni-icons>
      <text class="placeholder">搜索</text>
    </view>
  </view>
</template>

<script>
  export default {
    name:"my-search",
    data() {
      return {
        
      };
    },
    props:{
      bgcolor:{
        type:String,
        default:'#c00000'
      },
      radius:{
        type:Number,
        default:18
      }
      },
      methods:{
        searchBoxHandler(){
          this.$emit('click')
        }
    },
  }
</script>

<style lang="scss">
.my-search-container {
  background-color: #c00000;
  height: 50px;
  padding: 0 10px;
  display: flex;
  align-items: center;
  
  .my-search-box {
    height: 36px;
    background-color: #ffffff;
    width: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
    
    .placeholder {
      font-size: 15px;
      margin-left: 5px;
    }
    }
}

</style>