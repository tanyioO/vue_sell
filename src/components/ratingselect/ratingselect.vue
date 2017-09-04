<template>
  <div class="ratingselect border-1px">
    <div class="rating-type">
      <span @click="select(2,$event)" class="block posi" :class="{'active':selectType === 2}">
        {{desc.all}}
        <span class="count">{{ratings.length}}</span>
      </span>
      <span @click="select(0,$event)" class="block posi" :class="{'active':selectType === 0}">
        {{desc.positive}}
        <span class="count">{{positives.length}}</span>
      </span>
      <span @click="select(1,$event)" class="block nega" :class="{'active':selectType === 1}">
        {{desc.negative}}
        <span class="count">{{negatives.length}}</span>
      </span>
    </div>
    <div @click="togglecontent($event)" class="swith" :class="{'on':onlyContent}">
      <span class="icon-check_circle"></span>
      <span class="text">只看有内容的评价</span>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  /* eslint-disable no-unused-vars */


  const POSITIVE = 0
  const NEGATIVE = 1
  const ALL = 2

  export default {
    props: {
      ratings: {
        type: Array,
        default () {
          return []
        }
      },
      selectType: {
        type: Number,
        default: ALL
      },
      onlyContent: {
        type: Boolean,
        default: false
      },
      desc: {
        type: Object,
        default () {
          return {
            all: '全部',
            positive: '满意',
            negative: '不满意'
          }
        }
      }
    },
    methods: {
      select (type, event) {
        if (!event._constructed) {
          return
        }
        this.selectType = type
        this.$emit('changese', type)
      },
      togglecontent (event) {
        if (!event._constructed) {
          return
        }
        this.onlyContent = !this.onlyContent
        this.$emit('changeon', this.onlyContent)
      }
    },
    computed: {
      positives () {
        return this.ratings.filter((rating) => {
          return rating.rateType === POSITIVE
        })
      },
      negatives () {
        return this.ratings.filter((rating) => {
          return rating.rateType === NEGATIVE
        })
      },
    }
  }

</script>

<style lang="stylus" rel="stylesheet/stylus">

  @import "../../common/stylus/mixin.styl"

  .ratingselect
    .rating-type
      padding: 18px 0
      margin: 0 18px
      font-size: 0
      border-1px(rgba(7, 17, 27, 0.1))
      .block
        display: inline-block
        padding: 8px 12px
        line-height: 16px
        margin-right: 8px
        border-radius: 2px
        font-size: 12px
        color: rgb(77, 85, 93)
        .count
          font-size: 8px
          margin-left: 2px
        &.posi
          background: rgba(0, 160, 220, 0.2)
          &.active
            background: rgb(0, 160, 220)
            color: #fff
        &.nega
          background: rgba(77, 85, 93, 0.2)
          &.active
            background: rgb(77, 85, 93)
            color: #fff

    .swith
      padding: 12px 18px
      line-height: 30px
      border-bottom: 1px solid rgba(7, 17, 27, 0.2)
      font-size: 0
      color: rgb(147, 153, 159)
      .icon-check_circle
        display: inline-block
        vertical-align: top
        margin-right: 4px
        font-size: 30px
      .text
        display: inline-block
        vertical-align: top
        font-size: 12px
      &.on
        .icon-check_circle
          color: #00c850

</style>
