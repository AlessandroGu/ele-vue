<template>
<transition name="move">
  <div class="food" v-show="showFlag" ref="food">
    <div class="food-content">
      <div class="image-header">
        <img :src="item.image" alt="">
        <div class="back" @click="hide">
          <i class="icon-arrow_lift"></i>
        </div>
      </div>
      <div class="content">
          <h1 class="title">{{item.name}}</h1>
          <div class="detail">
              <span class="sell-count">月售{{item.sellCount}}份</span>
              <span class="rating">好评率{{item.rating}}%</span>
          </div>
          <div class="price">
              <span class="now">￥{{item.price}}</span>
              <span class="old" v-show="item.oldPrice">{{item.oldPrice}}</span>
          </div>
          <div class="cartcontrol-wrapper">
            <cartcontrol :item="item"></cartcontrol>
          </div>
          <div class="buy" @click.stop.prevent="addFirst($event)" v-show="!item.count || item.count === 0">加入购物车</div>
      </div>
    <split v-show="item.info"></split>
    <div class="info">
        <h1 class="title">商品信息</h1>
        <p class="text" v-show="item.info">{{item.info}}</p>
    </div>
    <split></split>
    <div class="rating">
        <h1 class="title">商品评价</h1>
        <ratingselect :select-type="selectType" :only-content="onlyContent" :desc="desc" :ratings="item.ratings" @rstType="rstType" @contToggle="contToggle"></ratingselect>
        <div class="rating-wrapper">
            <ul v-show="item.ratings && item.ratings.length">
                <li v-show="needShow(item.rateType, item.text)" v-for="(item, index) in item.ratings" :key="index" class="rating-item">
                    <div class="user">
                        <span class="name">{{item.username}}</span>
                        <img :src="item.avatar" alt="" class="avatar" width="12" height="12">
                    </div>
                    <div class="time">{{item.rateTime | formatDate}}</div>
                    <p class="text">
                        <span :class="{'icon-thumb_up': item.rateType === 0, 'icon-thumb_down': item.rateType === 1}">{{item.text}}</span>
                    </p>
                </li>
            </ul>
            <div class="no-rating" v-show="!item.ratings || !item.ratings.length">暂无评价</div>
        </div>
      </div>  
    </div>
  </div>
</transition>
</template>
<script>
const POSITIVE = 0;
const NEGATIVE = 1;
const ALL = 2;
import Bscroll from "better-scroll";
import Vue from "vue";
import cartcontrol from "../cartcontrol/cartcontrol.vue"
import split from "../split/split.vue"
import ratingselect from "../ratingselect/ratingselect.vue"
import {formatDate} from "../../common/js/date.js"
export default {
  props: {
    item: {
      type: Object
    }
  },
  data() {
    return {
      showFlag: false,
      selectType: ALL,
      onlyContent: false,
      desc: {
        all: '全部',
        positive: '推荐',
        negative: '吐槽'
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
  },
  methods: {
    show() {
      this.showFlag = true;
      this.selectType = ALL;
      this.onlyContent = true;
      this.$nextTick(() => {
        if (!this.scroll) {
          this.scroll = new Bscroll(this.$refs.food, {
            click: true
          })
        } else {
          this.scroll.refresh();
        }
      })
    },
    hide() {
      this.showFlag = false;
    },
    addFirst(event) {
      if (!event._constructed) {
        return;
      }
      this.$root.eventHub.$emit('cart.add', event.target); // 传输点击的目标元素
      Vue.set(this.item, 'count', 1);
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
    },
    rstType(type) {
      this.selectType = type;
      this.$nextTick(() => {
        this.scroll.refresh();
      })
    },
    contToggle(onlyContent) {
      this.onlyContent = onlyContent;
      this.$nextTick(() => {
        this.scroll.refresh();
      })
    }
  }
}
</script>
<style lang="stylus">
@import "../../common/stylus/mixin.styl";
  .food
    width: 100%
    background: #fff
    position: fixed
    left: 0
    top: 0
    bottom: 48px
    z-index: 30
    transition:all 0.3s
    &.move-enter-active
      transform:translate3d(0,0,0)
    &.move-enter,&.move-leave-to
      transform:translate3d(100%,0,0)
    .image-header
      position: relative
      width: 100%
      height: 0
      padding-bottom: 100%
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
          color: white
    .content
      padding: 18px
      .title
        line-height: 14px
        margin-bottom: 8px
        font-size: 14px
        font-weight: 700
        color: rgb(7, 17, 27)
      .detail
        margin-bottom: 18px
        height: 10px
        line-height: 10px
        font-size: 0
        .sell-count, .rating
          font-size: 10px
          color: rgb(147, 153, 159)
        .sell-count
          margin-right: 12px
      .price
        font-weight: 700
        line-height: 24px
        .now
          margin-right: 8px
          font-size: 14px
          color: rgb(240, 20, 20)
        .old
          text-decoration: line-through
          font-size: 10px
          color: rgb(147, 153, 159)
    .cartcontrol-wrapper
      position: absolute
      right: 12px
      bottom: 12px
    .buy
      position: absolute
      right: 18px
      bottom: 18px
      z-index: 10
      height: 24px
      line-height: 24px
      padding: 0 12px
      box-sizing: border-box
      border-radius: 12px
      font-size: 10px
      color: #ffffff
      background: rgb(0, 160, 220)
    .info
      padding: 18px
      .title
        line-height: 14px
        margin-bottom: 6px
        font-size: 14px
        color: rgb(7, 17, 27)
      .text
        line-height: 24px
        padding: 0 8px
        font-size: 12px
        color: rgb(77, 85, 93)
    .rating
      padding-top: 18px
      .title
        line-height: 14px
        margin-left: 18px
        font-size: 14px
        color: rgb(7, 17, 27)
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
            line-height: 12px
            font-size: 0
            .name
              display: inline-block
              margin-right: 6px
              vertical-align: top
              font-size: 10px
              color: rgb(147, 153, 159)
            .avatar
              border-radius: 50%
          .time
            margin-bottom: 6px
            line-height: 12px
            font-size: 10px
            color: rgb(147, 153, 159)
          .text
            line-height: 16px
            font-size: 12px
            color: rgb(7, 17, 27)
            .icon-thumb_up, .icon-thumb_down
              margin-right: 4px
              line-height: 16px
              font-size: 12px
            .icon-thumb_up
              color: rgb(0, 160, 220)
            .icon-thumb_down
              color: rgb(147, 153, 159)
        .no-rating
          padding: 16px 0
          font-size: 12px
          color: rgb(147, 153, 159)
</style>