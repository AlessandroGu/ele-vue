<template>
  <div class="cartcontrol">
    <transition name="move">
      <div class="cart-decrease" v-show="item.count > 0" @click.stop.prevent="decreaseCart">
          <span class="inner icon-remove_circle_outline"></span>
      </div>
    </transition>
    <div class="cart-count" v-show="item.count > 0">{{item.count}}</div>
    <div class="cart-add icon-add_circle" @click.stop.prevent="addCart($event)"></div>
  </div>
</template>
<script>
import Vue from "vue";
export default {
  props: {
    item: {
      type: Object
    }
  },
  methods: {
    addCart(event) {
      if (!event._constructed) {
        return;
      }
      if (!this.item.count) {
        Vue.set(this.item, 'count', 1);
      } else {
        this.item.count++;
      }
      this.$root.eventHub.$emit('cart.add', event.target);
    },
    decreaseCart() {
      if (this.item.count) {
        this.item.count--;
      }
    }
  }
}
</script>
<style lang="stylus">
  .cartcontrol
    font-size: 0
    .cart-decrease
      display: inline-block
      padding: 6px
      transition: all 0.4s linear
      .inner
        display:inline-block
        line-height: 24px
        font-size: 24px
        color: rgb(0, 160, 220)
        transition:all 0.4s linear
      &.move-enter-active
        opacity:1      
        transform:translate3d(0,0,0)// 硬件加速，动画更流畅
        .inner
          transform:rotate(0)
      &.move-enter,&.move-leave-to
        opacity:0
        transform:translate3d(24px,0,0)
        .inner
          transform:rotate(180deg)
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
      padding: 6px
      line-height: 24px
      font-size: 24px
      color: rgb(0, 160, 220)
</style>