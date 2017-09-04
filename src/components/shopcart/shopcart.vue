<template>
  <div class="wapper">
    <div class="shopcart">
      <div class="content" @click="togglelist">
        <div class="content-left">
          <div class="logo-wrapper">
            <div class="logo" :class="{'highlight':totalCount>0}">
              <i class="icon-shopping_cart" :class="{'highlight':totalCount>0}"></i>
            </div>
            <div class="num" v-show="totalCount>0">{{totalCount}}</div>
          </div>

          <div class="price" :class="{'highlight':totalPrice>0}">￥{{totalPrice}}</div>
          <div class="desc">另需配送费￥{{seller.deliveryPrice}}元</div>
        </div>
        <div class="content-right" @click.stop.prevent="pay">
          <div class="pay" :class="payEnough">{{payDesc}}</div>
        </div>
      </div>
      <div class="ball-container">
        <div v-for="ball in balls">
          <transition name="drop" @before-enter="beforeDrop" @enter="dropping" @after-enter="afterDrop">
            <div v-show="ball.show" class="ball">
              <div class="inner inner-hook"></div>
            </div>
          </transition>
        </div>
      </div>
      <div class="shopcart-list">
        <transition name="fold">
          <div v-show="listShow">
            <div class="list-header">
              <h1 class="title">购物车</h1>
              <span class="empty" @click="empty">清空</span>
            </div>
            <div class="list-content" ref="listContent">
              <ul>
                <li class="food" v-for="food in selectFoods">
                  <span class="name">{{food.name}}</span>
                  <div class="price">
                    <span>{{food.price * food.count}}</span>
                  </div>
                  <div class="cartcontrol-wrapper">
                    <cart-control :food="food" v-on:cartadd="drop"></cart-control>
                  </div>
                </li>
              </ul>
            </div>
          </div>
        </transition>
      </div>
    </div>
    <transition name="fade">
      <div class="list-mask" v-show="listShow" @click="listHide"></div>
    </transition>
  </div>
</template>

<script type="text/ecmascript-6">
  /* eslint-disable no-unused-vars */
  import cartcontrol from '../../components/cartcontrol/cartcontrol.vue'
  import BScroll from 'better-scroll'
  import goods from '../../components/goods/goods.vue'
  import Vue from 'vue'
  export default {
    props: {
      seller: {
        type: Object,
        default () {
          return {}
        }
      },
      selectFoods: {
        type: Array,
        default () {
          return []
        }
      }
    },
    data () {
      return {
        balls: [
          {
            show: false
          },
          {
            show: false
          },
          {
            show: false
          },
          {
            show: false
          },
          {
            show: false
          }
        ],
        dropBalls: [],
        fold: true
      }
    },
    methods: {
      togglelist () {
        if (!this.totalCount) {
          return
        }
        this.fold = !this.fold
      },

      pay () {
        if (this.totalPrice <this.seller.minPrice) {
          return
        }
        window.alert(`去支付￥${this.totalPrice}元`)
      },
      listHide () {
        this.fold = true
      },
      empty () {
        this.selectFoods.forEach((food) => {
          food.count = 0
        })
      },
      drop (target) {
        for (let i = 0; i < this.balls.length; i++) {
          let ball = this.balls[i]
          if (!ball.show) {
            ball.show = true
            ball.el = target
            this.dropBalls.push(ball)
            return
          }
        }
      },
      beforeDrop (el) { //这个方法的执行是因为这是一个vue的监听事件
        let count = this.balls.length
        while (count--) {
          let ball = this.balls[count]
          if (ball.show) {
            let rect = ball.el.getBoundingClientRect() //获取小球的相对于视口的位移(小球高度)
            let x = rect.left - 32
            let y = -(window.innerHeight - rect.top - 22) //负数,因为是从左上角往下的的方向
            el.style.display = '' //清空display
            el.style.webkitTransform = `translate3d(0,${y}px,0)`
            el.style.transform = `translate3d(0,${y}px,0)`
            //处理内层动画
            let inner = el.getElementsByClassName('inner-hook')[0] //使用inner-hook类来单纯被js操作
            inner.style.webkitTransform = `translate3d(${x}px,0,0)`
            inner.style.transform = `translate3d(${x}px,0,0)`
          }
        }
      },

      dropping (el, done) { //这个方法的执行是因为这是一个vue的监听事件
        /* eslint-disable no-unused-vars */
        let rf = el.offsetHeight //触发重绘html
        this.$nextTick(() => { //让动画效果异步执行,提高性能
          el.style.webkitTransform = 'translate3d(0,0,0)'
          el.style.transform = 'translate3d(0,0,0)'
          //处理内层动画
          let inner = el.getElementsByClassName('inner-hook')[0] //使用inner-hook类来单纯被js操作
          inner.style.webkitTransform = 'translate3d(0,0,0)'
          inner.style.transform = 'translate3d(0,0,0)'
          el.addEventListener('transitionend', done) //Vue为了知道过渡的完成，必须设置相应的事件监听器。
        })
      },

      afterDrop (el) { //这个方法的执行是因为这是一个vue的监听事件
        let ball = this.dropBalls.shift() //完成一次动画就删除一个dropBalls的小球
        if (ball) {
          ball.show = false
          el.style.display = 'none' //隐藏小球
        }
      }
    }
    ,
    computed: {
      totalPrice () {
        let total = 0
        this.selectFoods.forEach((food) => {
          total += food.price * food.count
        })
        return total
      }
      ,
      totalCount () {
        let count = 0
        this.selectFoods.forEach((food) => {
          count += food.count
        })
        return count
      }
      ,
      payDesc () {
        if (this.totalPrice === 0) {
          return `￥${this.seller.minPrice}元起送`
        } else if (this.seller.minPrice > this.totalPrice) {
          let diff = this.seller.minPrice - this.totalPrice
          return `还差￥${diff}元`
        } else {
          return '去结算'
        }
      }
      ,
      payEnough () {
        if (this.totalPrice >= this.seller.minPrice) {
          return 'enough'
        } else {
          return 'not-enough'
        }
      },
      listShow () {
        if (!this.totalCount) {
          this.fold = true
          return false
        }
        let show = !this.fold
        if (show) {
          this.$nextTick(() => {
            if (!this.scroll) {
              this.scroll = new BScroll(this.$refs.listContent, {
                click: true,
                probeType: 3
              })
            } else {
              this.scroll.refresh()
            }
          })
        }
        return show
      }
    },
    components: {
      'cart-control': cartcontrol,
    }
  }

</script>

<style lang="stylus" rel="stylesheet/stylus">

  @import "../../common/stylus/mixin.styl"

  .shopcart
    position: fixed
    left: 0
    bottom: 0
    width: 100%
    z-index: 50
    height: 48px
    .content
      display: flex
      font-size: 0
      background: #141d27
      .content-left
        flex: 1
        .logo-wrapper
          display: inline-block
          vertical-align: top
          position: relative
          top: -10px
          margin: 0 12px
          padding: 6px
          width: 56px
          height: 56px
          box-sizing: border-box
          border-radius: 50%
          background: #141d27
          .logo
            width: 100%
            height: 100%
            border-radius: 50%
            text-align: center
            background: #2b343c
            &.highlight
              background: rgb(0, 160, 220)
            .icon-shopping_cart
              font-size: 24px
              color: #80858a
              line-height: 44px
              &.highlight
                color: #fff
          .num
            position: absolute
            top: 0
            right: 0
            width: 24px
            height: 16px
            line-height: 16px
            text-align: center
            font-size: 9px
            border-radius: 16px
            font-weight: 700
            color: #fff
            background: rgb(240, 20, 20)
            box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.4)
        .price
          display: inline-block
          vertical-align: top
          margin-top: 12px
          line-height: 24px
          box-sizing: border-box
          padding-right: 12px
          border-right: 1px solid rgba(255, 255, 255, 0.1)
          font-size: 16px
          font-weight: 700
          color: rgba(255, 255, 255, 0.4)
          &.highlight
            color: #fff
        .desc
          display: inline-block
          vertical-align: top
          line-height: 24px
          margin-left: 12px
          margin-top: 12px
          font-size: 10px
          color: rgba(255, 255, 255, 0.4)
      .content-right
        flex: 0 0 80px
        width: 80px
        .pay
          height: 48px
          line-height: 48px
          text-align: center
          font-size: 10px
          font-weight: 700
          &.enough
            background: #00b43c
            color: #fff
          &.not-enough
            background: #2b333b
            color: rgba(255, 255, 255, 0.4)
    .ball-container
      .ball
        position: fixed
        left: 32px
        bottom: 22px
        z-index: 200
        transition: all 0.4s cubic-bezier(0.49, -0.29, 0.75, 0.41)
        .inner
          width: 16px
          height: 16px
          border-radius: 50%
          background: rgb(0, 160, 220)
          transition: all 0.4s linear

    .shopcart-list
      position: absolute
      left: 0
      bottom: 48px
      z-index: -1
      width: 100%
      .fold-enter-active, .fold-leave-active
        transition: all .5s

      .fold-enter, .fold-leave-to
        transform: translate3d(0, 100%, 0)

      .list-header
        height: 40px
        line-height: 40px
        padding: 0 18px
        background: #f3f5f7
        border-1px(rgba(7, 17, 27, 0.1))
        .title
          float: left
          font-size: 14px
          color: rgb(7, 17, 27)
        .empty
          float: right
          font-size: 12px
          color: rgb(0, 160, 220)
      .list-content
        padding: 0 18px
        max-height: 217px
        overflow: hidden
        background: #fff
        .food
          position: relative
          padding: 12px 0
          box-sizing: border-box
          border-1px(rgba(7, 17, 27, 0.1))
          .name
            line-height: 24px
            font-size: 14px
            color: rgb(7, 17, 27,)
          .price
            position: absolute
            right: 90px
            bottom: 12px
            line-height: 24px
            font-size: 14px
            font-weight: 700
            color: rgb(240, 20, 20)
          .cartcontrol-wrapper
            position: absolute
            bottom: 6px
            right: 0px

  .fade-enter-active, .fade-leave-active
    transition: opacity 0.3s

  .fade-enter, .fade-leave-to
    opacity: 0

  .list-mask
    position: fixed
    top: 0
    left: 0
    width: 100%
    height: 100%
    z-index: 40
    background: rgba(7, 17, 27, 0.6)
    -webkit-backdrop-filter: blur(10px)
</style>
