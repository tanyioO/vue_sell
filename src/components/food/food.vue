<template>
  <transition name="slide">
    <div class="food" v-if="showFlag" ref="food">
      <div class="food-content">
        <div class="img-header">
          <img :src="food.image" class="imag">
          <div class="back" @click="back">
            <i class="icon-arrow_lift"></i>
          </div>
        </div>
        <div class="content">
          <h1 class="title">{{food.name}}</h1>
          <div class="detail">
            <span class="sell-count">月售{{food.sellCount}}</span>
            <span class="rating">好评率{{food.rating}}%</span>
          </div>
          <div class="price">
            <span class="now">￥{{food.price}}</span><span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
          </div>
          <div class="cartcontrol-wrapper">
            <cart-control :food="food" @cartadd="_drop"></cart-control>
          </div>
          <transition name="fade">
            <div class="buy" @click.stop.prevent="addFirst" v-show="!food.count || food.count === 0">加入购物车</div>
          </transition>
        </div>
        <split v-show="food.info"></split>
        <div class="info" v-show="food.info">
          <h1 class="title">商品信息</h1>
          <p class="text">{{food.info}}</p>
        </div>
        <split></split>
        <div class="rating">
          <h1 class="title">商品评价</h1>
          <rating-select :select-type="selectType"
                         :only-content="onlyContent"
                         :desc="desc"
                         @changeon="changeOnlyContent"
                         @changese="changeSelectType"
                         :ratings="food.ratings"></rating-select>

          <div class="ratings-wrapper">
            <ul v-show="food.ratings && food.ratings.length">
              <li class="rating-item border-1px"
                  v-show="needShow(rating.rateType,rating.text)"
                  v-for="rating in food.ratings">
                <div class="user">
                  <span class="name">{{rating.username}}</span>
                  <img class="avatar" width="12" height="12" :src="rating.avatar">
                </div>
                <div class="time">{{new Date(rating.rateTime).toLocaleDateString()}}
                  {{new Date(rating.rateTime).getHours()}}:{{new Date(rating.rateTime).getMinutes()}}
                </div>
                <p class="text">
                  <span :class="{'icon-thumb_up':rating.rateType === 0,
                  'icon-thumb_down':rating.rateType === 1}"></span>{{rating.text}}
                </p>
              </li>
            </ul>
            <div class="no-rating"
                 v-show="!(food.ratings && food.ratings.length)">
              暂无评价
            </div>
          </div>
        </div>
      </div>
    </div>
  </transition>
</template>

<script type="text/ecmascript-6">
  /* eslint-disable no-unused-vars */

  import BScroll from 'better-scroll'
  import cartcontrol from '../../components/cartcontrol/cartcontrol.vue'
  import Vue from 'vue'
  import split from '../../components/split/split.vue'
  import ratingselect from '../../components/ratingselect/ratingselect.vue'
  //import { formatTime } from '../../common/js/date.js'
  const ALL = 2

  export default {
    props: {
      food: {
        type: Object
      }
    },
    data () {
      return {
        showFlag: false,
        selectType: ALL,
        onlyContent: true,
        desc: {
          all: '全部',
          positive: '推荐',
          negative: '吐槽'
        }
      }
    },
    methods: {
      show () {
        this.showFlag = true
        this.selectType = ALL
        this.onlyContent = true
        this.$nextTick(() => {
          if (!this.scoll) {
            this.scroll = new BScroll(this.$refs.food, {
              click: true
            })
          } else {
            this.scroll.refresh()
          }
        })
      },
      back () {
        this.showFlag = false
      },
      addFirst (event) {
        if (!event._constructed) {
          return
        }
        Vue.set(this.food, 'count', 1)
        this.$emit('cartadd', event.target)
      },
      _drop (target) {
        this.$emit('cartadd', target)
      },
      needShow (type, text) {
        if (this.onlyContent && !text) {
          return false
        } else if (this.selectType === ALL) {
          return true
        } else {
          return type === this.selectType
        }
      },
      changeOnlyContent (onlyContent) {
        this.onlyContent = onlyContent
        this.$nextTick(() => {
          this.scroll.refresh()
        })
      },
      changeSelectType (type) {
        this.selectType = type
        this.$nextTick(() => {
          this.scroll.refresh()
        })
      }
    },
    /* 没有用到的<时间磋格式化>过滤器
    fliters:{
      formatDate(time){
        let oldtime= new Date(time);
        return formatTime(oldtime,'yyyy-MM-dd hh:mm')
      }
    },
    formatTime（）方法为正则检查函数
    */
    components: {
      'cart-control': cartcontrol,
      'split': split,
      'rating-select': ratingselect
    }
  }

</script>
<style lang="stylus" rel="stylesheet/stylus">

  @import "../../common/stylus/mixin.styl"

  .slide-enter-active, .slide-leave-active
    transition: all 0.5s linear

  .slide-enter, .slide-leave-to
    transform: translate3d(100%, 0, 0)

  .food
    position: fixed
    left: 0
    top: 0
    bottom: 48px
    z-index: 30
    width: 100%
    background: #fff
    .img-header
      position: relative
      width: 100%
      height: 0
      padding-top: 100%
      .imag
        position: absolute
        left: 0
        top: 0
        width: 100%
        height: 100%
      .back
        position: absolute
        top: 10px
        left: 0
        .icon-arrow_lift
          display: block
          padding: 10px
          font-size: 20px
          color: #fff
    .content
      position: relative
      padding: 18px
      .title
        line-height: 14px
        margin-bottom: 8px
        font-size: 14px
        font-weight: 700
        color: rgb(7, 17, 27)
      .detail
        height: 10px
        margin-bottom: 18px
        line-height: 10px
        font-size: 0
        .sell-count, .rating
          font-size: 10px
          color: rgb(147, 153, 159)
        .sell-count
          margin-right: 12px
      .price
        line-height: 24px
        font-weight: 700
        .now
          margin-right: 8px
          color: rgb(240, 20, 20)
          font-size: 14px
        .old
          text-decoration: line-through
          font-size: 10px
          color: rgb(147, 153, 159)

      .cartcontrol-wrapper
        position: absolute
        right: 12px
        bottom: 12px
      .fade-enter-active, .fade-leave-active
        transition: opacity 0.2s
      .fade-enter, .fade-leave-to
        opacity: 0
      .buy
        position: absolute
        right: 18px
        bottom: 18px
        z-index: 10
        height: 24px
        line-height: 24px
        padding: 0 12px
        box-sizing: border-box
        font-size: 10px
        border-radius: 12px
        color: #fff
        background: rgb(0, 160, 220)

    .info
      padding: 18px
      .title
        line-height: 14px
        font-size: 14px
        margin-bottom: 6px
        color: rgb(7, 17, 27)
      .text
        padding-left: 0 8px
        line-height: 24px
        font-size: 12px
        color: rgb(77, 85, 93)
    .rating
      padding-top: 18px
      .title
        line-height: 14px
        font-size: 14px
        margin-left: 18px
        color: rgb(7, 17, 27)
    .ratings-wrapper
      padding: 0 18px
      .rating-item
        position: relative
        padding: 16px 0
        border-1px(rgba(7, 17, 27, 0.1))
        .user
          position: absolute
          line-height: 12px
          right: 0
          top: 16px
          font-size: 0
          .name
            display: inline-block
            vertical-align: top
            margin-right: 6px
            font-size: 10px
            color: rgb(147, 153, 159)
          .avatar
            display: inline-block
            vertical-align: top
            border-radius: 50%
        .time
          line-height: 12px
          margin-bottom: 6px
          font-size: 10px
          color: rgb(147, 153, 159)
        .text
          line-height: 16px
          font-size: 12px
          color: rgb(7, 17, 27)
          .icon-thumb_up, .icon-thumb_down
            line-height: 16px
            margin-right: 4px
            font-size: 12px
          .icon-thumb_up
            color: rgb(0, 160, 220)
          .icon-thumb_down
            color: rgb(147, 153, 159)

      .no-rating
        padding: 16px
        font-size: 12px
        color: rgb(147, 153, 159)
</style>
