<template>
  <div class="cartcontrol">
    <transition name="move">
      <div class="cart-decrease" v-show="food.count>0"
           @click.stop.prvent="decreaseCart">
        <span class="inner icon-remove_circle_outline"></span>
      </div>
    </transition>
    <div class="cart-count" v-show="food.count>0">{{food.count}}</div>
    <div class="cart-add icon-add_circle" @click.stop.prvent="addCart"></div>
  </div>
</template>

<script type="text/ecmascript-6">
  /* eslint-disable no-unused-vars */
  import Vue from 'vue'
  export default {
    props: {
      food: {
        type: Object
      }
    },
    data () {
      return {
        etarget: {}
      }
    },
    methods: {
      addCart (event) {
        if (!event._constructed) {
          return
        }
        if (!this.food.count) {
          Vue.set(this.food, 'count', 1)
        } else {
          this.food.count++
        }
        this.$emit('cartadd', event.target)
      },
      decreaseCart () {
        if (!event._constructed) {
          return
        }

        if (this.food.count) {
          this.food.count--
        }
      }
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">

  .cartcontrol
    font-size: 0

  .cart-decrease
    display: inline-block
    padding: 6px
    .inner
      display: inline-block
      line-height: 24px
      font-size: 24px
      color: rgb(0, 160, 220)

  .move-enter-active, .move-leave-active
    transition: all 0.3s linear

  .move-enter, .move-leave-to
    transform: translate3d(24px, 0, 0)
    opacity: 0

  .cart-count
    display: inline-block
    vertical-align: top
    width: 12px
    padding-top: 6px
    line-height: 24px
    text-align: center
    font-size: 10px
    color: rgb(147, 153, 159)

  .cart-add
    display: inline-block
    line-height: 24px
    font-size: 24px
    padding: 6px
    color: rgb(0, 160, 220)


</style>
