<template>
   <div class="ratings" ref="rating">
      <div class="rating-content">
        <div class="overview">
           <div class="overview-left">
              <h1 class="score">{{seller.score}}</h1>
              <div class="title">综合评分</div>
              <div class="rank">高于周边商家{{seller.rankRate}}</div>
           </div>
           <div class="overview-right">
              <div class="score-wrapper">
                <span class="title">服务态度</span>
                <star :size="36" :score="seller.serviceScore"></star>
                <div class="score">{{seller.serviceScore}}</div>
              </div>
              <div class="score-wrapper">
                <span class="title">商品评分</span>
                <star :size="36" :score="seller.foodScore"></star>
                <div class="score">{{seller.foodScore}}</div>
              </div>
              <div class="delivery-wrapper">
                <span class="title">送达时间</span>
                <span class="delivery">{{seller.deliveryTime}}分钟</span>
              </div>
           </div>   
        </div>
        <split></split>
        <ratingselect @select="selectRating" @toggle="toggleContent" :selectType="selectType" :onlyContent="onlyContent"  :ratings="ratings"></ratingselect>
        <div class="rating-wrapper">
          <ul>
            <li v-for="rating in ratings" class="rating-item" v-show="needShow(rating.rateType,rating.text)">
              <div class="avatar">
                <img width="28" height="28" :src="rating.avatar" alt="">
              </div>
              <div class="content">
                <h1 class="name">{{rating.username}}</h1>
                <div class="star-wrapper">
                  <star :size="24"  :score="rating.score"></star>
                  <span class="delivery" v-show="ratings.deliveryTime">{{rating.deliveryTime}}</span>
                </div>
                <p class="text">{{rating.text}}</p>
                <div class="recommend" v-show="rating.recommend && rating.recommend.length">
                   <span class="icon-thumb_up"></span>
                   <span class="item" v-for="item in rating.recommend">{{item}}</span>
                </div>
                <div class="time">
                  {{rating.rateTime|formatDate}}
                </div>
              </div>
            </li>
          </ul>
        </div>
      </div>
   </div>
</template>
<script type="text/ecmascript-6">
import axios from 'axios'
import star from '../star/star'
import split from '../split/split'
import BScroll from 'better-scroll'
import { formatDate } from '../../common/js/date.js'
import ratingselect from '../ratingselect/ratingselect'
const ALL = 2
const ERR_OK = 200
export default {
  props: {
    seller: {
      type: Object
    }
  },
  data () {
    return {
      showFlag: false,
      selectType: ALL,
      onlyContent: true,
      ratings: []
    }
  },
  created () {
    axios.get('/api/ratings').then(response => {
      if (response.status === ERR_OK) {
        this.ratings = response.data.data
        this.$nextTick(() => {
          if (!this.scroll) {
            this.scroll = new BScroll(this.$refs.rating, {
              click: true
            })
          } else {
            this.scroll.refresh()
          }
        })
      }
    })
    .catch(error => {
      console.log(error)
      // alert('网络错误，不能访问')
    })
  },
  methods: {
    selectRating (type) {
      this.selectType = type
      this.$nextTick(() => {
        this.scroll.refresh()
      })
    },
    toggleContent () {
      this.onlyContent = !this.onlyContent
      this.$nextTick(() => {
        this.scroll.refresh()
      })
    },
    needShow (type, text) {
      if (this.onlyContent && !text) {
        return false
      }
      if (this.selectType === ALL) {
        return true
      } else {
        return type === this.selectType
      }
    }
  },
  components: {
    star,
    split,
    ratingselect
  },
  filters: {
    formatDate (time) {
      let date = new Date(time)
      return formatDate(date, 'yyyy-MM-dd hh:mm')
    }
  }
}
</script>
<style lang="scss" scoped>
@import "../../common/scss/mixin.scss";
.ratings{
   position: absolute;
   top: 174px;
   bottom: 0;
   left: 0;
   width: 100%;
   overflow: hidden;
   .overview{
      display: flex;
      padding: 18px 0;
      .overview-left{
        flex: 0 0 137px;
        padding-bottom: 6px;
        width: 137px;
        border-right: 1px solid rgba(7,17,27,0.2);
        text-align: center;
        @media only screen and (max-width: 320px){
          flex: 0 0 120px;
          width: 120px;    
        }
        .score{
          margin-bottom: 6px;
          line-height: 28px;
          font-size: 24px;
          color: rgb(255, 153, 0)
        }
        .title{
          margin-bottom: 8px;
          line-height: 12px;
          font-size: 12px;
          color: rgb(7, 17, 27)
        }
        .rank{
          line-height: 10px;
          font-size: 10px;
          color: rgb(147, 153, 159)
        }
      }
      .overview-right{
         flex: 1;
         padding: 6px 0 6px 24px;
         @media only screen and (max-width: 320px){
           padding-left:6px;    
        }
         .score-wrapper{
           margin-bottom: 8px;
           font-size: 0;
           .title{
              display: inline-block;
              line-height: 18px;
              vertical-align: top;
              font-size: 12px;
              color: rgb(7, 17, 27)
           }
           .star{
              display: inline-block;
              vertical-align: top;   
              margin: 0 12px;
           }
           .score{
              display: inline-block;
              line-height: 18px;
              vertical-align: top;
              font-size: 12px;
              color: rgb(7, 17, 27)
           }
         }
         .delivery-wrapper{
           font-size: 0;
           .title{
              line-height: 18px;
              font-size: 12px;
              color: rgb(7, 17, 27)
           }
           .delivery{
              margin-left: 12px;
              font-size: 12px;
              color: rgb(147, 153, 159)
           }
         }
      }
   }
   .rating-wrapper{
     padding: 0 18px;
     .rating-item{
       display: flex;
       padding: 18px 0;
       @include border-1px(rgba(7,17,27,0.1));
       &:last-child{
         @include border-none()
       }
       .avatar{
         flex:0 0 28px;
         width: 28px;
         margin-right: 12px;
         img{
           border-radius: 50%;
         }
       }
       .content{
         position: relative;
         flex: 1;
         .name{
           line-height: 12px;
           font-size: 10px;
           color: rgb(7,17,27);
           margin-bottom:4px;
         }
         .star-wrapper{
           margin-bottom: 6px;
           font-size: 0;
           .star{
             display: inline-block;
             vertical-align: top;
             margin-right: 6px;
           }
           .delivery{
             display: inline-block;
             vertical-align: top;
             line-height: 12px;
             font-size: 10px;
             color: rgb(147,153,159);
           }
         }
         .text{
           margin-bottom: 8px;
           line-height: 18px;
           color: rgb(7,17,27);
           font-size: 12px;
         }
         .recommend{
           line-height: 16px;
           font-size: 0;
           .icon-thumb_up,.item{
             display: inline-block;
             margin: 0 8px 4px 0;
             font-size: 9px;
           }
           .icon-thumb_up{
             color: rgb(0 ,160, 220);
           }
           .item{
             padding: 0 6px;
             border: 1px solid rgba(7,17,27,0.2);
             border-radius: 1px;
             color:rgb(147,153,159);
             background: #fff;
           }
         }
         .time{
           position: absolute;
           top: 0;
           right: 0;
           line-height: 12px;
           font-size: 10px;
           color:rgb(147, 153, 159);
         }
       }
     }
   }
}
</style>


