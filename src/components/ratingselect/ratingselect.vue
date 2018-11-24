<template>
  <div class="ratingselect">
    <div class="rating-type">
      <span @click="select(2, $event)" class="block all" :class="{'active': selectType === 2}">
        {{ desc.all }}
        <span class="count">{{ ratings.length }}</span>
      </span>
      <span @click="select(0, $event)" class="block positive" :class="{'active': selectType === 0}">
        {{ desc.positive }}
        <span class="count">{{ this.positives.length }}</span>
      </span>
      <span @click="select(1, $event)" class="block negative" :class="{'active': selectType === 1}">
        {{ desc.negative }}
        <span class="count">{{ this.negative.length }}</span>
      </span>
    </div>
    <div class="switch" :class="{'on': onlyContent}" @click="toggleContent">
      <span class="icon-check_circle"></span>
      <span class="text">只看内容评价</span>
    </div>
  </div>
</template>

<script>

  import Bus from '../../common/js/EventBus'

  /*
  *  使代码可读性 更强
  * */
  const POSITIVE = 0
  const NEGATIVE = 1
  const ALL = 2

  export default {
    name: "ratingselect",
    props: {
      /*
      * 默认值 是对象或数组
      *   返回值 用函数 返回
      * */
      ratings: {
        type: Array,
        default() {
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
        default() {
          return {
            all: '全部',
            positive: '满意',
            negative: '不满意'
          }
        }
      }
    },
    data() {
      return {
        selectType2: this.selectType,
        onlyContent2: this.onlyContent
      }
    },
    computed: {
      positives() {
        return this.ratings.filter((rating) => {
          return rating.rateType === POSITIVE
        })
      },
      negative() {
        return this.ratings.filter((rating) => {
          return rating.rateType === NEGATIVE
        })
      }
    },
    methods: {
      select(type, event) {
        if (!event._constructed) {
          return;
        }
        this.selectType2 = type;
        Bus.$emit('select', type);

      },
      toggleContent(event) {
        if (!event._constructed) {
          return
        }
        this.onlyContent2 = !this.onlyContent2
        Bus.$emit('toggle', this.onlyContent2)
      }
    }

  }
</script>

<style lang='stylus' scoped>

  @import '../../common/stylus/mixin.styl'

  .ratingselect
    .rating-type
      padding: 18px 0
      margin: 0 18px
      border-1px(rgba(7, 17, 27, 0.1))
      .block
        padding: 8px
        font-size: 12px
        line-height: 18px
        margin-right: 6px
        border-radius: 2px
        color: rgb(77, 85, 93)
        &.active
          color: #fff
        .count
          font-size: 8px
          margin-left: 2px
      .positive, .all
        background-color: rgba(0, 160, 220, 0.2)
        &.active
          background-color: rgb(0, 160, 220)
      .negative
        background-color: rgba(77, 85, 93, 0.2)
        &.active
          background-color: rgb(77, 85, 93)
    .switch
      padding: 12px 18px
      font-size: 0
      line-height: 24px
      color: rgb(147, 153, 159)
      border-1px(rgba(7, 17, 27, 0.1))
      &.on
        .icon-check_circle
          color: #00c850
      .icon-check_circle
        font-size: 24px
        display: inline-block
        vertical-align: top
      .text
        position: relative
        margin-left: 4px
        font-size: 12px
        display: inline-block
        vertical-align: top


</style>
