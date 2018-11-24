<template>

    <transition name="move">
      <div v-show="showFlag" class="food" ref="food">
        <div class="food-content">
          <div class="img-header">
            <img :src="food.image">
            <div class="back" @click="hide">
              <i class="icon-arrow_lift"></i>
            </div>
          </div>
          <div class="content">
            <h1 class="title">{{ food.name }}</h1>
            <div class="detail">
              <span class="sell-count">月售{{ food.sellCount }}份</span>
              <span class="rating">好评率{{ food.rating }}%</span>
            </div>
            <div class="price">
              <span class="now">￥{{ food.price }}</span>
              <span v-show="food.oldPrice" class="old">￥{{ food.oldPrice }}</span>
            </div>
            <div class="cartcontrol-wrapper">
              <cartcontrol :food="food"></cartcontrol>
            </div>
            <transition name="fade">
              <div @click.stop.prevent="addFirst($event)" class="buy" v-show=" !food.count || food.count === 0">加入购物车</div>
            </transition>
          </div>
          <split v-show="food.info"></split>
          <div class="info" v-show="food.info">
            <h1 class="title">商品介绍</h1>
            <div class="text">{{ food.info }}</div>
          </div>
          <split></split>
          <div class="rating">
            <div class="title">商品评价</div>
            <ratingselect :selectType="selectType" :onlyContent="onlyContent" :desc="desc" :ratings="food.ratings"></ratingselect>
            <div class="rating-wrapper">
              <ul v-show="food.ratings && food.ratings.length">
                <li v-show="needShow(rating.rateType, rating.text)" v-for="rating in food.ratings" class="rating-item">
                  <div class="user">
                    <span class="name">{{ rating.username }}</span>
                    <img :src="rating.avatar" class="avatar" width="12" height="12">
                  </div>
                  <div class="time">{{ rating.rateTime | formatDate }}</div>
                  <p class="text">
                    <span :class="{'icon-thumb_up':rating.rateType===0,'icon-thumb_down':rating.rateType===1}"></span>
                    {{ rating.text }}
                  </p>
                </li>
              </ul>
              <div class="no-rating" v-show=" !food.ratings || !food.ratings.length ">暂无评价</div>
            </div>
          </div>
        </div>
      </div>
    </transition>

</template>

<script>

  import BScroll from 'better-scroll'
  import cartcontrol from '../cartcontrol/cartcontrol'
  import ratingselect from '../ratingselect/ratingselect'
  import split from '../split/split'
  import Vue from 'vue'
  import Bus from '../../common/js/EventBus'
  // 导出的模块 不是 export default
  // 所以要加 {} 如果导出多个函数 {A,B, ...}
  import {formatDate} from '../../common/js/date'

  const POSITIVE = 0
  const NEGATIVE = 1
  const ALL = 2

  export default {
    name: "food",
    props: {
      food: {
        type: Object
      }
    },
    data() {
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
    mounted() {
      Bus.$on('select', selectType => {
        this.selectType = selectType;
        this.$nextTick(() => {
          this.scroll.refresh();
        })
      })

      Bus.$on('toggle', onlyContent => {
        this.onlyContent = onlyContent
        this.$nextTick(() => {
          this.scroll.refresh()
        })
      })
    },
    methods: {
      show() {
        this.showFlag = true
        this.selectType = ALL
        this.onlyContent = true
        this.$nextTick(() => {
          if (!this.scroll) {
            this.scroll = new BScroll(this.$refs.food, {
              click: true
            })
          } else {
            // 重新计算
            this.scroll.refresh()
            // console.log(this.scroll.refresh())
          }
        })
      },
      hide() {
        this.showFlag = false
      },
      addFirst(event) {
        if (!event._constructed) {
          return
        }
        Vue.set(this.food, 'count', 1)
      },
      needShow(type, text) {
        // 先过滤是否 为空文本
        if (this.onlyContent && !text) {
          return false
        }
        // 再过滤显示内容的类型
        if (this.selectType === ALL) {
          return true
        } else {
          return type === this.selectType
        }
      }
    },
    filters: {
      formatDate(time) {
        let date = new Date(time);
        return formatDate(date, 'yyyy-MM-dd hh:mm');
      }
    },
    components: {
      cartcontrol,
      split,
      ratingselect
    }
  }
</script>

<style lang='stylus' scoped>

  @import '../../common/stylus/mixin.styl'

  .food
    position: fixed
    left: 0
    top: 0
    bottom: 48px
    z-index: 30
    width: 100%
    background-color: #fff
    &.move-enter-active, &.move-leave-active
      transition: all .2s linear
    &.move-enter, &.move-leave-to
      transform: translate3d(100%, 0, 0)
    .food-content
      .img-header
        position: relative
        width: 100%
        height: 0
        // 黑魔法
        // 当 padding 设为 100% 此时是相对于 宽度来说的
        padding-top: 100%
        img
          position: absolute
          top: 0
          left: 0
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
        padding: 18px
        position: relative
        .title
          line-height: 14px
          font-size: 14px
          margin-bottom: 8px
          font-weight: 700
          color: rgb(7, 17, 27)
        .detail
          margin-bottom: 18px
          line-height: 10px
          height: 10px
          font-size: 0
          .sell-count, .rating
            font-size: 10px
            color: rgb(147, 153, 159)
          .sell-count
            margin-right: 12px
        .price
          line-height: 24px
          font-weight: 700
          font-size: 0
          .now
            font-size: 14px
            font-weight: 700
            color: rgb(240, 20, 20)
            margin-right: 8px
          .old
            font-size: 10px
            color: rgb(147, 153, 159)
            text-decoration: line-through
            vertical-align: top
        .cartcontrol-wrapper
          position: absolute
          right: 30px
          bottom: 14px
        .buy
          position: absolute
          right: 20px
          bottom: 20px
          z-index: 10
          line-height: 24px
          padding: 0 12px
          -webkit-box-sizing: border-box
          -moz-box-sizing: border-box
          box-sizing: border-box
          font-size: 10px
          border-radius: 12px
          background-color: rgb(0, 160, 220)
          color: #fff
          &.fade-enter-active, &.fade-leave-active
            transition: opacity .5s
          &.fade-enter, &.fade-leave-to
            opacity: 0

      .info
        padding: 18px
        .title
          font-size: 14px
          font-weight: 500
          color: rgb(7, 17, 27)
        .text
          margin-top: 6px
          padding: 0 8px
          font-size: 12px
          font-weight: 200
          line-height: 24px
          color: rgb(77, 85, 93)

      .rating
        // 因为设计稿中 有一条贯穿横轴的线 所以不能取 padding:18px
        padding-top: 18px
        .title
          font-size: 14px
          font-weight: 500
          color: rgb(7, 17, 27)
          margin-left: 18px
        .rating-wrapper
          padding: 0 18px
          .rating-item
            position: relative
            padding: 16px 0
            border-1px(rgba(7, 17, 27, 0.1))
            .user
              position: absolute
              right: 0
              top: 16px
              font-size: 0
              line-height: 12px
              .name
                display: inline-block
                vertical-align: top
                font-size: 10px
                color: rgb(147, 153, 159)
              .avatar
                margin-left: 6px
                border-radius: 50%

            .time
              margin-bottom: 6px
              font-size: 10px
              color: rgb(147, 153, 159)
              line-height: 12px
            .text
              line-height: 16px
              font-size: 12px
              color: rgb(7, 17, 27)
              .icon-thumb_up, .icon-thumb_down
                padding-right: 4px
                font-size: 12px
                line-height: 16px
              .icon-thumb_down
                color: rgb(147, 153, 159)
              .icon-thumb_up
                color: rgb(0, 160, 220)
          .no-rating
            padding: 16px 0
            font-size: 12px
            color: rgb(147, 153, 159)
</style>
