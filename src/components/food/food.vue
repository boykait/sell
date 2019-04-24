<template>
  <transition name="move">
    <div v-show="showFlag" class="food" ref="foodRef">
      <div class="food-content">
        <div class="image-header">
          <img :src="food.image">
          <div class="back" @click="hide">
            <i class="icon-arrow_lift"></i>
          </div>
        </div>
        <div class="content">
          <h1 class="title">{{food.name}}</h1>
          <div class="detail">
            <span class="sell-count">月售{{food.sellCount}}</span>
            <span class="rating">好评率{{food.rating}}%</span>
          </div>
          <div class="price">
            <span class="now">￥{{food.price}}</span><span class="old" v-show="food.oldPrice">￥{{food.v}}</span>
          </div>
          <div class="cartcontrol-wrapper">
            <cart-control :food="food"></cart-control>
          </div>
          <transition name="fade">
            <div class="buy" v-show="!food.count || food.count === 0"
                 @click="addFirst()">加入购物车</div>
          </transition>
        </div>
        <split></split>
        <div class="info" v-show="food.info">
          <h1 class="title">商品信息</h1>
          <p class="text">{{food.info}}</p>
        </div>
        <split></split>
        <div class="rating">
          <h1 class="title">商品评价</h1>
          <rating-select :select-type="selectType"
          :only-content="onlyContent"
          :desc="desc"
          :ratings="food.ratings"></rating-select>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
import BScroll from 'better-scroll';
import cartControl from '../cartcontrol/cartcontrol';
import split from '../split/split';
import ratingSelect from '../ratingselect/ratingselect';
const POSITIVE = 0;
const NEGATIVE = 1;
const ALL = 2;

export default {
  name: 'food',
  props: {
    food: {
      type: Object
    }
  },
  components: {
    'cart-control': cartControl,
    split,
    'rating-select': ratingSelect
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
    };
  },
  methods: {
    show() {
      this.showFlag = true;
      this.selectType = ALL;
      this.onlyContent = true;
      this.$nextTick(() => {
        if (!this.foodScroll) {
          this.foodScroll = new BScroll(this.$refs.foodRef, {
            probeType: 3,
            click: true
          });
        } else {
          this.foodScroll.refresh();
        }
      });
    },
    hide() {
      this.showFlag = false;
    },
    addFirst() {
      this.$set(this.food, 'count', 1);
    }
  }
};
</script>

<style scoped lang="scss">
  .move-enter-active, .move-leave-active {
    transition: all 0.2s linear;
    transform: translate3d(0, 0, 0);
  }
  .move-enter, .move-leave {
    transform: translate3d(100%, 0, 0);
  }
  .food {
    position: fixed;
    left: 0;
    top: 0;
    bottom: 48px;
    z-index: 30;
    width: 100%;
    background: #fff;
    .food-content {
      position: relative;
      .image-header {
        position: relative;
        width: 100%;
        height: 0;
        padding-top: 100%;
        img {
          position: absolute;
          top: 0;
          left: 0;
          width: 100%;
          height: 100%;
        }
        .back {
          position: absolute;
          top: 10px;
          left: 0;
          .icon-arrow_left {
            display: block;
            padding: 10px;
            font-size: 20px;
            color: #fff;
          }
        }
      }
      .content {
        padding: 18px;
        position: relative;
        .title {
          line-height: 14px;
          margin-bottom: 8px;
          font-size: 14px;
          font-weight: 700;
          color: rgb(7, 17, 26);
        }
        .detail {
          margin-bottom: 18px;
          line-height: 10px;
          height: 10px;
          font-size: 0;
          .sell-count, .rating {
            font-size: 10px;
            color: rgb(147, 153, 159);
          }
          .sell-count {
            margin-right: 12px;
          }
        }
        .price {
          font-weight: 700;
          font-size: 24px;
          .now {
            margin-right: 18px;
            font-size: 14px;
            color: rgb(240, 20, 20);
          }
          .old {
            text-decoration: line-through;
            font-size: 10px;
            color: rgb(147, 153, 159);
          }
        }
        .cartcontrol-wrapper {
          position: absolute;
          right: 12px;
          bottom: 12px;
        }
        .fade-enter-active, .fade-leave-active {
          transition: opacity .5s
        }
        .fade-enter, .fade-leave-active {
          opacity: 0
        }
        .buy {
          position: absolute;
          right: 18px;
          bottom: 18px;
          z-index: 10;
          height: 24px;
          line-height: 24px;
          padding: 0 12px;
          box-sizing: border-box;
          font-size: 10px;
          border-radius: 12px;
          color: #fff;
          background: rgb(0, 160, 220);
          transition: all 1.2s linear;
        }
      }
      .info {
        padding: 18px;
        .title {
          line-height: 14px;
          margin-bottom: 6px;
          font-size: 14px;
          color: rgb(77, 85, 93);
        }
        .text {
          line-height: 24px;
          padding: 0 8px;
          font-size: 12px;
          color: rgb(77, 85, 93);
        }
      }
    }
    .rating {
      padding-top: 18px;
      .title {
        line-height: 14px;
        margin-left: 6px;
        font-size: 14px;
        color: rgb(77, 85, 93);
      }
    }
  }
</style>
