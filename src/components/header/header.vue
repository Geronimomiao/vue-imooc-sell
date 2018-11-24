<template>
  <div class="header">
    <div class="content-wrapper">
      <div class="avatar">
        <img width="64" height="64" :src="seller.avatar">
      </div>
      <div class="content">
          <div class="title">
            <span class="brand"></span>
            <span class="name">{{ seller.name }}</span>
          </div>
          <div class="description">
            {{ seller.description }}/{{ seller.deliveryTime }}分钟送达
          </div>
          <div v-if="seller.supports" class="support">
            <span class="icon" :class="classMap[seller.supports[0].type]"></span>
            <span class="text">{{ seller.supports[0].description }}</span>
          </div>
        </div>
      <div v-if="seller.supports" class="support-count" @click="showDetail">
          <span class="count">{{ seller.supports.length }}个</span>
          <i class="icon-keyboard_arrow_right"></i>
        </div>
    </div>
    <div class="bulletin-wrapper" @click="showDetail">
      <span class="bulletin-title"></span>
      <span class="bulletin-text">{{ seller.bulletin }}</span>
      <i class="icon-keyboard_arrow_right"></i>
    </div>
    <div class="background">
      <img :src="seller.avatar" height="100%" width="100%">
    </div>
    <transition name="fade">
      <div class="detail" v-show="detailShow">
      <div class="detail-wrapper clearfix">
        <div class="detail-main">
          <h1 class="name">{{ seller.name }}</h1>
          <div class="star-wrapper">
            <star :size="48" :score="seller.score"></star>
          </div>
          <div class="title">
            <div class="line"></div>
            <div class="text">优惠信息</div>
            <div class="line"></div>
          </div>
          <ul v-if="seller.supports" class="supports">
            <li class="support-item" v-for="item in seller.supports">
              <span class="icon" :class="classMap[item.type]"></span>
              <span class="text">{{ item.description }}</span>
            </li>
          </ul>
          <div class="title">
            <div class="line"></div>
            <div class="text">商家公告</div>
            <div class="line"></div>
          </div>
          <div class="bulletin">
            <p class="content">{{ seller.bulletin }}</p>
          </div>
        </div>
      </div>
      <div class="detail-close" @click="hideDetail">
        <i class="icon-close"></i>
      </div>
    </div>
    </transition>
  </div>
</template>

<script>
    import star from '../star/star'

    export default {
      name: "header",
      props: {
        seller: {
          type: Object
        }
      },
      created() {
        this.classMap = ['decrease','discount','special','invoice','guarantee']
      },
      data() {
        return {
          detailShow: false
        }
      },
      methods: {
        showDetail() {
          this.detailShow = true
        },
        hideDetail() {
          this.detailShow = false
        }
      },
      components: {
        star
      }
    }
</script>

<style lang="stylus" scoped>

  @import "../../common/stylus/mixin.styl"
  @import "../../common/stylus/base.styl"

  .header
    color #fff
    background-color rgba(7,17,27,0.5)
    blur 10px
    position relative
    overflow hidden
    .content-wrapper
      padding 24px 12px 18px 24px
      font-size 0px
      position relative
      .avatar
        display inline-block
        vertical-align top
        img
         border-radius 2px
      .content
        display inline-block
        margin-left 16px
        vertical-align top
        .title
          margin 2px 0 8px 0
          .brand
            margin-bottom -3px
            display inline-block
            width 30px
            height 18px
            bg-image('brand')
            background-size 30px 18px
            background-repeat no-repeat
          .name
            font-size 16px
            font-weight bold
            margin-left 6px
            line-height 16px

        .description
          font-size 12px
          margin-top 8px
          line-height 12px
          margin-bottom 10px

        .support
          .icon
            display: inline-block
            vertical-align: top
            width: 12px
            height: 12px
            margin-right: 4px
            -webkit-background-size: 12px 12px
            background-size: 12px 12px
            background-repeat: no-repeat
            &.decrease
              bg-image('decrease_1')
            &.discount
              bg-image('discount_1')
            &.guarantee
              bg-image('guarantee_1')
            &.invoice
              bg-image('invoice_1')
            &.special
              bg-image('special_1')

        .text
          line-height: 12px
          font-size: 10px
      .support-count
        position: absolute
        right: 12px
        bottom: 18px
        padding: 0 8px
        height: 24px
        line-height: 24px
        border-radius: 14px
        background-color: rgba(0, 0, 0, 0.2)
        text-align: center
        .count
          font-size: 10px
          vertical-align: top
          margin-left: 6px
          line-height: 24px
        .icon-keyboard_arrow_right
          font-size: 10px
          margin-left: 2px
          line-height: 24px

    .bulletin-wrapper
      height: 28px
      line-height: 28px
      padding: 0 22px 0 12px
      white-space: nowrap
      overflow: hidden
      text-overflow: ellipsis
      background-color: rgba(7, 17, 27, 0.2)
      position: relative
      .bulletin-title
        display: inline-block
        width: 22px
        height: 12px
        bg-image('bulletin')
        margin-bottom: -2px
        -webkit-background-size: 22px 12px
        background-size: 22px 12px
        background-repeat: no-repeat
      .bulletin-text
        margin: 0 4px
        font-size: 10px
        font-weight: 200
      .icon-keyboard_arrow_right
        position: absolute
        font-size: 10px
        right: 12px
        top: 9px
    .background
      position: absolute
      z-index: -1
      top: 0
      left: 0
      width: 100%
      height: 100%
      filter: blur(10px)
    .detail
      position: fixed
      top: 0
      left: 0
      z-index: 100
      width: 100%
      height: 100%
      overflow: auto
      transition: all 0.5s
      background: rgba(7, 17, 27, .8)
      backdrop-filter: blur(10px)
      &.fade-enter-active, &.fade-leave-active
        transition: opacity .5s
      &.fade-enter, &.fade-leave-active
        opacity: 0
      .detail-wrapper
        width: 100%
        min-height: 100%
        .detail-main
          margin-top: 64px
          padding-bottom: 64px
          .name
            line-height: 16px
            text-align: center
            font-size: 16px
            font-weight: 700
            margin: 0 auto
          .star-wrapper
            margin-top: 18px
            padding: 2px 0
            text-align: center
            .star-item:last-child
              margin-right: 16px
           .title
             display: flex
             width: 80%
             margin: 28px auto 24px auto
             .line
               flex: 1
               position: relative
               top: -6px
               border-bottom: 1px solid rgba(255, 255, 255, 0.2)
             .text
               padding: 0 12px
               font-size: 14px
               font-weight: 700
           .supports
             width: 80%
             margin: 0 auto
             .support-item
               padding: 0 12px
               margin-bottom: 12px
               font-size: 0
               &:last-child
                 margin-bottom: 0
               .icon
                 display: inline-block
                 width: 16px
                 height: 16px
                 vertical-align: top
                 margin-right: 6px
                 background-size: 16px 16px
                 background-repeat: no-repeat
                 &.decrease
                   bg-image('decrease_2')
                 &.discount
                   bg-image('discount_2')
                 &.guarantee
                   bg-image('guarantee_2')
                 &.invoice
                   bg-image('invoice_2')
                 &.special
                   bg-image('special_2')

               .text
                 line-height: 16px
                 font-size: 12px
           .bulletin
             width: 80%
             padding: 0 12px
             margin: 24px auto 0 auto
             .content
               font-size: 12px
               font-weight: 200
               line-height: 24px
               color: rgb(255, 255, 255)
      .detail-close
        position: relative
        width: 32px
        height: 32px
        margin: -64px auto 0 auto
        clear: both
        font-size: 32px

</style>
