<template>
    <div class="ratings" ref="ratings">
      <div class="ratings-content">
        <div class="overview">
          <div class="overview-left">
            <h1 class="score">{{ seller.score }}</h1>
            <div class="title">综合评分</div>
            <div class="rank">高于周边商家{{ seller.rankRate }}%</div>
          </div>
          <div class="overview-right">
            <div class="service">
              <span class="left">服务态度</span>
              <star :size="36" :score="seller.serviceScore"></star>
              <span class="score">{{ seller.serviceScore }}</span>
            </div>
            <div class="food">
              <span class="left">食品评价</span>
              <star :size="36" :score="seller.foodScore"></star>
              <span class="score">{{ seller.foodScore }}</span>
            </div>
            <div class="time">
              <span class="left">送达时间</span>
              <span class="text">{{ seller.deliveryTime }}分钟</span>
            </div>
          </div>
        </div>
        <split></split>
        <ratingselect :selectType="selectType" :onlyContent="onlyContent" :ratings="ratings" @select="select" @onlyContent2="onlyContent2"></ratingselect>
        <div class="rating-wrapper">
          <ul>
            <li v-for="rating in ratings" class="rating-item" v-show="needShow(rating.rateType, rating.text)">
              <div class="avatar">
                <img :src="rating.avatar" width="28" height="28">
              </div>
              <div class="content">
                <h1 class="name">{{ rating.username }}</h1>
                <div class="star-wrapper">
                  <star :size="24" :score="rating.score"></star>
                  <div class="delivery" v-show="rating.deliveryTime"></div>
                </div>
                <p class="text">{{ rating.text }}</p>
                <div class="recommend" v-show="rating.recommend && rating.recommend.length">
                  <span class="icon-thumb_up"></span>
                  <span v-for="item in rating.recommend" class="item">{{ item }}</span>
                </div>
                <div class="time">{{  rating.rateTime | formatDate  }}</div>
              </div>
            </li>
          </ul>
        </div>
      </div>
    </div>
</template>

<script>

  import star from '../star/star'
  import ratingselect from '../ratingselect/ratingselect'
  import split from '../split/split'
  import {formatDate} from '../../common/js/date'
  import BScroll from 'better-scroll'

  const ALL = 2

  export default {
    name: "ratings",
    props: {
      seller: {
        type: Object
      }
    },
    data() {
      return {
        ratings: [],
        selectType: ALL,
        onlyContent: true
      }
    },
    created() {
      this.$http.get('/api/ratings').then((res) => {
        res = res.body
        this.ratings = res.data
        this.$nextTick(() => {
          this.scroll = new BScroll(this.$refs.ratings, {
            click: true
          })
        })
      })
    },
    methods: {
      select(type) {
        this.selectType = type;
        this.$nextTick(() => {
          this.scroll.refresh();
        });
      },
      onlyContent2(onlyContent) {
        this.onlyContent = onlyContent;
        this.$nextTick(() => {
          this.scroll.refresh()
        });
      },
      needShow(type, text) {
        if (this.onlyContent && !text) {
          return false;
        }
        if (this.selectType === ALL) {
          return true;
        } else {
          return type === this.selectType;
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
      star,
      split,
      ratingselect
    }
  }
</script>

<style lang='stylus' scoped>

  @import '../../common/stylus/mixin.styl'

  .ratings
    position: absolute
    top: 174px
    left: 0
    bottom: 0
    width: 100%
    overflow: hidden
    .overview
      display: flex
      padding: 18px
      @media only screen and (max-width: 370px)
        padding: 18px 6px
      .overview-left
        flex: 0 0 137px
        // 兼容性
        width: 137px
        border-right: 1px solid rgba(7, 17, 27, 0.1)
        text-align: center
        @media only screen and (max-width: 370px)
          flex: 0 0 120px
          width: 120px
        .score
          line-height: 28px
          font-size: 24px
          color: rgb(255, 153, 0)
          margin-bottom: 6px
        .title
          margin-bottom: 8px
          line-height: 12px
          font-size: 12px
          color: rgb(7, 17, 27)
        .rank
          font-size: 10px
          line-height: 10px
          color: rgba(7, 17, 27, 0.4)
      .overview-right
        flex: 1
        padding-left: 24px
        @media only screen and (max-width: 370px)
          padding-left: 6px
        .service, .food, .time
          font-size: 0
          line-height: 18px
          .left
            margin-bottom: 8px
            font-size: 12px
            color: rgb(7, 17 ,27)
            margin-right: 12px
          .score
            color: rgb(255, 135, 0)
            font-size: 12px
            vertical-align: top
          .text
            font-size: 12px
            color: rgba(7, 17, 27, 0.4)
          .star
            display: inline-block
            margin-right: 8px
            vertical-align: sub

    .rating-wrapper
      padding: 0 18px
      .rating-item
        padding: 18px 0
        position: relative
        border-1px(rgba(7, 17, 27, 0.1))
        .avatar
          position: absolute
          img
            border-radius: 50%
        .content
          margin-left: 40px
          .name
            margin-bottom: 4px
            font-size: 10px
            color: rgb(7, 17, 27)
            line-height: 12px
          .star-wrapper
            font-size: 0
            .star
              margin-bottom: 6px
              margin-right: 6px
            .delivery
              font-size: 10px
              font-weight: 200
              line-height: 12px
              color: rgb(147, 153, 159)

          .text
            margin-bottom: 8px
            font-size: 12px
            color: rgb(7, 17, 27)
            line-height: 18px
          .recommend
            font-size: 0
            .icon-thumb_up
              margin-right: 8px
              font-size: 12px
              line-height: 16px
              color: rgb(0, 160, 220)
            .item
              display: inline-block
              padding: 0 6px
              margin-right: 8px
              font-size: 9px
              line-height: 16px
              border: 1px solid rgba(7, 17, 27, 0.1)
              border-radius: 2px
              background-color: #fff
              color: rgb(147, 153, 159)
              &:last-child
                margin-right: 0
          .time
            position: absolute
            top: 18px
            right: 0
            font-size: 10px
            font-weight: 200
            line-height: 12px
            color: rgb(147, 153, 159)

</style>
