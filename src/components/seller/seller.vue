<template>
  <div class="seller" ref="seller">
    <div class="seller-content">
      <div class="overview">
        <h1 class="title">{{ seller.name }}</h1>
        <div class="desc">
          <star :size="36" :score="seller.score"></star>
          <span class="text">({{ seller.ratingCount }})</span>
          <span class="text">月售{{ seller.sellCount }}单</span>
        </div>
        <div class="body">
          <div class="block">
            <span class="top">起送价</span>
            <span class="bottom">
              <span class="num">{{ seller.minPrice }}</span>元
            </span>
          </div>
          <div class="block">
            <span class="top">商家配送</span>
            <span class="bottom">
              <span class="num">{{ seller.deliveryPrice }}</span>元
            </span>
          </div>
          <div class="block">
            <span class="top">平均配送时间</span>
            <span class="bottom">
              <span class="num">{{ seller.deliveryTime }}</span>分钟
            </span>
          </div>
        </div>
        <div class="icon-wrapper" @click="fav">
          <span class="icon-favorite icon-favorite-1" v-show="favorite"></span>
          <span class="icon-favorite icon-favorite-2" v-show="!favorite"></span>
          <span class="icon-text" v-show="favorite">已收藏</span>
          <span class="icon-text" v-show="!favorite">未收藏</span>
        </div>
      </div>
      <split></split>
      <div class="announcement" v-show="seller.supports">
        <h1 class="title">公告与活动</h1>
        <div class="desc">{{ seller.bulletin }}</div>
        <ul>
          <li v-for="item in seller.supports" class="item">
            <span class="icon" :class="classMap[item.type]"></span>
            <span class="text">{{ item.description }}</span>
          </li>
        </ul>
      </div>
      <split v-show="seller.supports"></split>
      <div class="pics">
        <h1 class="title">商家实景</h1>
        <div class="pic-wrapper" ref="picWrapper">
          <ul class="pic-list" ref="picList">
            <li class="pic-item" v-for="pic in seller.pics">
              <img :src="pic" width="120" height="90">
            </li>
          </ul>
        </div>
      </div>
      <split></split>
      <div class="message">
        <h1 class="title">商家信息</h1>
        <ul>
          <li v-for="msg in seller.infos" class="msg">{{ msg }}</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>

  import BScroll from 'better-scroll'
  import star from '../star/star'
  import split from '../split/split'
  import shopcart from '../shopcart/shopcart'

  export default {
    name: "seller",
    props: {
      seller: {
        type: Object
      }
    },
    data() {
      return {
        favorite: false
      }
    },
    created() {
      this.classMap = ['decrease','discount','special','invoice','guarantee']
    },
    mounted() {
      this.$nextTick(() => {
        this.scroll = new BScroll(this.$refs.seller, {
          click: true
        })
      })
      if (this.seller.pics) {
        let picWidth = 120
        let margin = 6
        let width = (picWidth + margin) * this.seller.pics.length - margin
        this.$refs.picList.style.width = width + 'px'
        this.$nextTick(() => {
          this.picScroll = new BScroll(this.$refs.picWrapper, {
            scrollX: true,
            eventPassthrough: 'vertical'
          })
        })
      }
    },
    methods: {
      fav() {
        this.favorite = !this.favorite
      }
    },
    components: {
      star,
      split,
      shopcart
    }
  }

</script>

<style lang='stylus' scoped>

  @import "../../common/stylus/mixin.styl"

  .seller
    position: absolute
    width: 100%
    top: 174px
    bottom: 0
    left: 0
    overflow: hidden
    .seller-content
      .overview
        padding: 18px
        position: relative
        border-1px(rgba(7, 17, 27, 0.1))
        .title
          margin-bottom: 8px
          font-size: 14px
          line-height: 14px
          font-weight: 700
          color: rgb(7, 17, 27)
        .desc
          padding-bottom: 18px
          border-1px(rgba(7, 17, 27, 0.1))
          .star
            margin-right: 8px
            display: inline-block
          .text
            vertical-align: text-top
            margin-right: 12px
            font-size: 10px
            line-height: 18px
            color: rgb(77, 85, 93)
        .body
          display: flex
          .block
            flex: 1
            margin-top: 18px
            text-align: center
            border-right: 1px solid rgba(7, 17, 27, 0.1)
            &:last-child
              border-right: 0
            .top
              display: block
              font-size: 10px
              color: rgb(147, 153, 159)
              margin-bottom: 4px
            .bottom
              font-size: 10px
              color: rgb(7, 17, 27)
              font-weight: 200
              line-height: 24px
              .num
                font-size: 24px
        .icon-wrapper
          position: absolute
          top: 18px
          right: 18px
          display: flex
          flex-direction: column
          text-align: center
          .icon-favorite
            font-size: 24px
            line-height: 24px
            flex: 0 0 28px
          .icon-favorite-1
            color: rgb(240, 20, 20)
          .icon-favorite-2
            color: rgba(7, 17, 27, 0.1)
          .icon-text
            font-size: 10px
            line-height: 10px
            color: rgb(77, 85, 93)

      .announcement
        padding: 18px
        .title
          font-size: 14px
          line-height: 14px
          font-weight: 700
          color: rgb(7, 17, 27)
        .desc
          padding: 12px
          font-size: 12px
          line-height: 24px
          color: rgb(240, 20, 20)
          border-1px(rgba(7, 17, 27, 0.1))
        .item
          padding: 16px 12px
          border-1px(rgba(7, 17, 27, 0.1))
          &:last-child
            padding-bottom: 0
            border-1px(rgba(7, 17, 27, 0))
          .icon
            vertical-align: top
            width: 16px
            height: 16px
            display: inline-block
            background-repeat: no-repeat
            -webkit-background-size: 16px 16px
            background-size: 16px 16px
            &.decrease
              bg-image('decrease_4')
            &.discount
              bg-image('discount_4')
            &.guarantee
              bg-image('guarantee_4')
            &.invoice
              bg-image('invoice_4')
            &.special
              bg-image('special_4')
          .text
            font-size: 12px
            font-weight: 200
            color: rgb(7, 17, 27)
            line-height: 16px
            vertical-align: bottom

      .pics
        padding: 18px
        .title
          margin-bottom: 8px
          font-size: 14px
          line-height: 14px
          font-weight: 700
          color: rgb(7, 17, 27)
        .pic-wrapper
          width: 100%
          overflow: hidden
          /* 当内容超出视口宽度不折行 */
          white-space: nowrap
          .pic-list
            font-size: 0
            .pic-item
              display: inline-block
              margin-right: 6px
              width: 120px
              height: 90px
              &:last-child
                margin-right: 0
      .message
        padding: 18px
        .title
          padding-bottom: 12px
          font-size: 14px
          line-height: 14px
          font-weight: 700
          color: rgb(7, 17, 27)
          border-1px(rgba(7, 17, 27, 0.1))
        .msg
          font-size: 14px
          font-weight: 200
          line-height: 16px
          color: rgb(7, 17, 27)
          padding: 16px 12px
          border-1px(rgba(7, 17, 27, 0.1))
          &:last-child
            padding-bottom: 0
            border-1px(rgba(7, 17, 27, 0))


</style>
