<template>
  <div id="app">
     <v-header :seller="seller"></v-header>
     <div class="tab border-1px">
        <div class="tab-item">
         <router-link to="/goods" class="tab-item">商品</router-link>
       </div>
       <div class="tab-item">
         <router-link to="/ratings" class="tab-item">评论</router-link>
       </div>
       <div class="tab-item">
         <router-link to="/seller" class="tab-item">商家</router-link>
       </div>
     </div>
     <keep-alive>
       <router-view :seller="seller"></router-view>
     </keep-alive>
  </div>
</template>
<script type="text/ecmascript-6">
import {urlParse} from './common/js/util'
import header from './components/header/header'
import axios from 'axios'
const ERR_OK = 200
// const debug = process.env.NODE_ENV !== 'production'
export default {
  name: 'app',
  components: {
    'v-header': header
  },
  data () {
    return {
      seller: {
        id: (() => {
          let queryParm = urlParse()
          return queryParm.id
        })()
      },
      goods: [],
      ratings: []
    }
  },
  created () {
   // const url = debug ? '/api/seller' : 'http://ustbhuangyi.com/sell/api/seller'
    axios
      .get('/api/seller' + '?id=' + this.seller.id)
      .then(response => {
        if (response.status === ERR_OK) {
          this.seller = Object.assign({}, this.seller, response.data.data)
        }
      })
      .catch(error => {
        console.log(error)
        alert('网络错误，不能访问')
      })
    axios
      .get('/api/goods')
      .then(response => {
        if (response.status === ERR_OK) {
          this.goods = response.data.data
        }
      })
      .catch(error => {
        console.log(error)
        alert('网络错误，不能访问')
      })
    axios
      .get('/api/ratings')
      .then(response => {
        if (response.status === ERR_OK) {
          this.ratings = response.data.data
        }
      })
      .catch(error => {
        console.log(error)
        alert('网络错误，不能访问')
      })
  }
}
</script>

<style lang="scss" scoped>
@import "./common/scss/mixin.scss";
#app .tab {
  display: flex;
  width: 100%;
  height: 40px;
  line-height: 40px;
  /* border-bottom: 1px solid rgba(7, 17, 27, 0.1); */
  @include border-1px (rgba(7, 17, 27, 0.1));
  .tab-item {
    flex: 1;
    text-align: center;
    & > a {
      display: block;
      font-size: 14px;
      color: rgb(77, 85, 93);
      &.active {
        color: rgb(240, 20, 20);
      }
    }
  }
}
</style>

