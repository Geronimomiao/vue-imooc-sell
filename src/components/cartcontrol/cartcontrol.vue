<template>
  <div class="cartcontroll">
    <transition name="move">
      <div class="cart-decrease" v-show="food.count>0" @click="decreaseCart">
        <span class="inner icon-remove_circle_outline"></span>
      </div>
    </transition>
    <div class="cart-count" v-show="food.count>0">{{ food.count }}</div>
    <div class="cart-increase icon-add_circle" @click="addCart"></div>
  </div>
</template>

<script>

  import Vue from 'vue'
  import Bus from '../../common/js/EventBus'

  export default {
    name: "cartcontrol",
    props: {
      food: {
        type: Object
      }
    },
    methods: {
      addCart() {
        if (!this.food.count) {
          // 通过该方法 给对象 添加一个新属性 当该属性值发生变化时 可以被自动观测到
          Vue.set(this.food, 'count', 1)
        } else {
          this.food.count++
        }
        Bus.$emit('cart.add', event.target)
      },
      decreaseCart() {
        this.food.count--
      }
    }
  }
</script>

<style lang='stylus' scoped>

  .cartcontroll
    font-size: 0
    .cart-decrease
      display: inline-block
      padding: 6px
      .inner
        font-size: 24px
        line-height: 24px
        color: rgb(0, 160, 220)
      &.move-enter-active, &.move-leave-active
        transition: all .5s
        transform: translate3d(0, 0, 0)
        .inner
          display: inline-block
          transition: all .5s
          transform: rotate(0deg)
      &.move-enter, &.move-leave-to
        opacity: 0
        transform: translate3d(24px, 0, 0)
        .inner
          // 否则 高度为0 无法展示 滚动效果
          display: inline-block
          transform: rotate(180deg)
    .cart-count
      display: inline-block
      vertical-align: top
      padding-top: 5px
      // 左右的按钮 有padding
      width: 12px
      font-size: 10px
      line-height: 24px
      color: rgb(147, 153, 159)
    .cart-increase
      display: inline-block
      font-size: 24px
      line-height: 36px
      color: rgb(0, 160, 220)

</style>
