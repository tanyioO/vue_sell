<template>
  <div class="goods">
    <div class="menu">
      <ul>
        <li v-for="(item,index) in goods" class="menu-item" @click="selectMenu(index,$event)"
            v-bind:class="{'current':currentIndex===index}">
          <span class="text border-1px">
            <span class="icon" v-show="item.type > 0" :class="classMap[item.type]"></span>
            {{item.name}}
          </span>
        </li>
      </ul>
    </div>
    <div class="foods">
      <ul>
        <li v-for="item in goods" class="food-list food-list-hook">
          <h1 class="title">{{item.name}}</h1>
          <ul>
            <li v-for="food in item.foods" class="food-item border-1px" @click="selectedFood(food,$event)">
              <div class="icon">
                <img width="57" height="57" :src="food.icon">
              </div>
              <div class="content">
                <h1 class="name">{{food.name}}</h1>
                <p class="desc" v-if="food.description">{{food.description}}</p>
                <div class="extra">
                  <span class="count">月售{{food.sellCount}}份</span><span>好评{{food.rating}}%</span>
                </div>
                <div class="price">
                  <span class="now">￥{{food.price}}</span><span class="old"
                                                                v-show="food.oldPrice">￥{{food.oldPrice}}</span>
                </div>
                <div class="cart-wrapper">
                  <cart-control :food="food" @cartadd="_drop"></cart-control>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <shop-cart ref="shopcart" v-bind:seller="seller"
               v-bind:select-foods="selectFoods"></shop-cart>
    <food-detail v-bind:food="selectFood" ref="food" @cartadd="_drop"></food-detail>
  </div>
</template>

<script type="text/ecmascript-6">
  /* eslint-disable no-unused-vars */

  import BScroll from 'better-scroll'
  import shopcart from '../shopcart/shopcart.vue'
  import cartcontrol from '../../components/cartcontrol/cartcontrol.vue'
  import food from '../../components/food/food.vue'

  const ERR_OK = 0

  export default {
    props: {
      seller: {
        type: Object,
        default () {
          return {}
        }
      }
    },

    data () {
      return {
        goods: [],
        listHeight: [],
        scrollY: 0,
        selectFood: {}
      }
    },
    computed: {
      currentIndex () {
        for (let i = 0; i < this.listHeight.length; i++) {
          let height1 = this.listHeight[i]
          let height2 = this.listHeight[i + 1]
          if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
            return i
          }
        }
        return 0
      },
      selectFoods () {
        let foods = []
        this.goods.forEach((good) => {
          good.foods.forEach((food) => {
            if (food.count) {
              foods.push(food)
            }
          })
        })
        this.$emit('shopdata',foods)
        return foods
      }
    },
    created () {

      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']

      this.$http.get('/api/goods').then((response) => {
        response = response.body
        if (response.errno === ERR_OK) {
          this.goods = response.data
          this.$nextTick(() => {
            this._initScroll()
            this._calculateHeight()
          })
        }
      })
    },
    methods: {
      selectedFood (food,event) {
        this.selectFood = food;
        this.$refs.food.show();
      },
      selectMenu (index, event) {
        if (event._constructed = false) {
          return
        }
        let foodsWrapper = document.querySelector('.foods')
        let foodlist = foodsWrapper.getElementsByClassName('food-list-hook')
        let el = foodlist[index]
        this.foodsScroll.scrollToElement(el, 300)
      },
      _initScroll () {
        let menuWrapper = document.querySelector('.menu')
        let foodsWrapper = document.querySelector('.foods')
        this.menuScroll = new BScroll(menuWrapper, {
          click: true
        })
        this.foodsScroll = new BScroll(foodsWrapper, {
          click: true,
          probeType: 3
        })
        this.foodsScroll.on('scroll', (pos) => {
          this.scrollY = Math.abs(Math.round(pos.y))
        })
      },
      _calculateHeight () {
        let foodsWrapper = document.querySelector('.foods')
        let foodlist = foodsWrapper.getElementsByClassName('food-list-hook')
        let height = 0
        this.listHeight.push(height)
        for (let i = 0; i < foodlist.length; i++) {
          let item = foodlist[i]
          height += item.clientHeight
          this.listHeight.push(height)
        }
      },
      _drop (target) {
        this.$nextTick(() => {
          this.$refs.shopcart.drop(target)
        })
      }
    },
    components: {
      'shop-cart': shopcart,
      'cart-control': cartcontrol,
      'food-detail':food
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">

  @import "../../common/stylus/mixin.styl"

  .goods
    display: flex
    position: absolute
    top: 174px
    bottom: 46px
    width: 100%
    overflow: hidden
    .menu
      flex: 0 0 80px
      height: 100%
      width: 80px
      padding-top: 1px
      background: #f3f5f7
      .menu-item
        display: table
        width: 56px
        font-size: 0
        height: 54px
        padding: 0 12px
        line-height: 14px
        &.current
          position: relative
          z-index: 10
          margin-top: -1px
          background: rgba(7, 17, 27, 0.1)
          font-weight: 700
          .text
            border-none()
        .icon
          vertical-align: top
          display: inline-block
          width: 12px
          height: 12px
          margin-right: 2px
          background-size 12px 12px
          background-repeat: no-repeat
          &.decrease
            bg-image('decrease_3')
          &.discount
            bg-image('discount_3')
          &.guarantee
            bg-image('guarantee_3')
          &.invoice
            bg-image('invoice_3')
          &.special
            bg-image('special_3')
        .text
          display: table-cell
          width: 56px
          font-size: 12px
          vertical-align: middle
          border-1px(rgba(7, 17, 27, 0.1))
    .foods
      flex: 1
      height: 100%
      .title
        padding-left: 14px
        height: 26px
        line-height: 26px
        border-left: 2px solid red
        font-size: 12px
        clolor: rgb(147, 153, 159)
        background: #f3f5f7
      .food-item
        display: flex
        margin: 18px
        padding-bottom: 18px
        border-1px(rgba(7, 17, 27, 0.1))
        &:last-child
          border-none()
          margin-bottom: 0
        .icon
          flex: 0 0 57px
          margin-right: 10px
        .content
          flex: 1
          .name
            margin: 2px 0 8px 0
            height: 14px
            line-height: 14px
            font-size: 14px
            color: rgb(7, 17, 27)
            white-space: nowrap
            overflow: hidden
            text-overflow: ellipsis
          .desc, .extra
            line-height: 10px
            font-size: 10px
            color: rgb(147, 153, 159)
          .desc
            margin-bottom: 8px
            line-height: 14px
          .extra
            .count
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
          .cart-wrapper
            position: absolute
            right: 0
            bottom: 12px
</style>
