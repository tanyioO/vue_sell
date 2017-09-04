<template>
  <div id="app">
    <div class="header">
      <v-header v-bind:seller="seller"></v-header>
    </div>

    <div class="tab border-1px">
      <div class="tab-item">
        <router-link to="/goods">商品</router-link>
      </div>
      <div class="tab-item">
        <router-link to="/ratings">评价</router-link>
      </div>
      <div class="tab-item">
        <router-link to="/seller">商家</router-link>
      </div>
    </div>
    <keep-alive>
      <router-view v-bind:seller="seller" @shopdata="setFoods"></router-view>
    </keep-alive>

  </div>
</template>

<script type="text/ecmascript-6">
  /* eslint-disable no-unused-vars */
  import header from './components/header/header.vue'
  import Vue from 'vue'
  import { urlParse } from './common/js/unit'

  const ERR_OK = 0

  export default {
    data () {
      return {
        seller: {
          id: (() => {
            let x = urlParse()
            return x.id
          })()
        },
        foods: []
      }
    },
    created () {
      this.$http.get('/api/seller?id' + this.seller.id).then((response) => {
        response = response.body
        if (response.errno === ERR_OK) {
          this.seller = Object.assign({}, this.seller, response.data)
        }
      })
    },
    methods: {
      setFoods (fooddata) {
        this.foods = fooddata
      }
    },
    components: {
      'v-header': header

    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">

  @import "./common/stylus/mixin"

  #app
    .tab
      display: flex
      width: 100%
      height: 40px
      line-height: 40px
      border-1px(rgba(7, 17, 27, 0.3))
      .tab-item
        flex: 1
        text-align: center
        & > a
          display: block
          font-size: 14px
          line-height: 28px
          color: rgb(77, 85, 93)
          &.active
            color: rgb(240, 20, 20)


</style>
