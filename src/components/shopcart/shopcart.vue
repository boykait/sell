<template>
  <div class="shopcart">
    <div class="content" @click="toggleList">
      <div class="content-left">
        <div class="logo-wrapper">
          <div class="logo" :class="{'highlight' : totalCount > 0}">
            <span class="icon-shopping_cart" :class="{'highlight' : totalCount > 0}"></span>
          </div>
          <div class="num" v-show="totalCount > 0">{{totalCount}}</div>
        </div>
        <div class="price">￥ {{totalPrice}}</div>
        <div class="desc">另需配送费￥{{deliveryPrice}}</div>
      </div>
      <div class="content-right">
        <div class="pay" :class="payClass">
          {{payDesc}}
        </div>
      </div>
      <!--<div class="ball-container">-->
        <!--<div v-for="ball in balls" v-show="ball.show">-->
          <!--<transition name="drop" v-text="xxxxx">-->
            <!--<div :class="ball.show">-->
              <!--<div class="inner inner-hook"></div>-->
            <!--</div>-->
          <!--</transition>-->
        <!--</div>-->
      <!--</div>-->
      <div class="ball-container" v-for="(ball, index) in balls" :key="index">
        <!-- 过度钩子函数 -->
        <transition
          @before-enter="beforeEnter"
          @enter="enter"
          @after-enter="afterEnter">
          <div class="ball" v-show="ball.show">
            <div class="inner inner-hook"></div>
          </div>
        </transition>
      </div>
      <transition name="fold">
        <div class="shopcart-list" v-show="listShow">
          <div class="list-header">
            <h1 class="title">购物车</h1>
            <span class="empty">清空</span>
          </div>
          <div class="list-content" ref="listContent">
            <ul>
              <li class="food"
                  v-for="food in selectFoods">
                <span class="name">{{food.name}}</span>
                <div class="price">
                  <span>￥{{food.price * food.count}}</span>
                </div>
                <div class="cartcontrol-wrapper">
                  <cart-control :food="food"></cart-control>
                </div>
              </li>
            </ul>
          </div>
        </div>
      </transition>

    </div>
  </div>
</template>

<script>
import Cartcontrol from '../cartcontrol/cartcontrol';
import BScroll from 'better-scroll';
export default {
  name: 'shopchart',
  components: {
    'cart-control': Cartcontrol,
    BScroll
  },
  props: {
    deliveryPrice: {
      type: Number,
      default: 0
    },
    minPrice: {
      type: Number,
      default: 0
    },
    selectFoods: {
      type: Array,
      default() {
        return [{price: 10, count: 3}];
      }
    }
  },
  data() {
    return {
      balls: [
        {
          show: false
        },
        {
          show: false
        },
        {
          show: false
        },
        {
          show: false
        },
        {
          show: false
        }
      ],
      dropBalls: [],
      fold: true
    };
  },
  computed: {
    totalPrice() {
      return this.selectFoods.reduce((a, b) => a + b.price * b.count, 0);
    },
    totalCount() {
      return this.selectFoods.reduce((a, b) => a + b.count, 0);
    },
    payDesc() {
      if (this.totalPrice === 0) {
        return `￥${this.minPrice}元起送`;
      } else if (this.totalPrice < this.minPrice) {
        return `还差￥${this.minPrice - this.totalPrice}起送`;
      } else {
        return '去结算';
      }
    },
    payClass() {
      return this.totalPrice < this.minPrice ? 'not-enough' : 'enough';
    },
    listShow() {
      if (!this.totalCount) {
        this.fold = true;
        return false;
      }
      let show = !this.fold;
      if (show) {
        this.$nextTick(() => {
          if (!this.scroll) {
            this.scroll = new BScroll(this.$refs.listContent, {
              click: true
            });
          } else {
            this.scroll.refresh();
          }
        });
      }
      return show;
    }
  },
  methods: {
    drop(el) {
      console.log(el);
      for (let i = 0; i < this.balls.length; i += 1) {
        let ball = this.balls[i];
        if (!ball.show) {
          ball.show = true;
          ball.el = el;
          this.dropBalls.push(ball);
          return;
        }
      }
    },
    beforeEnter(el) {
      let count = this.balls.length;
      while (count--) {
        let ball = this.balls[count];
        if (ball.show) {
          let rect = ball.el.getBoundingClientRect();
          let x = rect.left - 32;
          let y = -(window.innerHeight - rect.top - 22);
          el.style.display = '';
          el.style.webkitTransform = `translate3d(0, ${y}px, 0)`;
          el.style.transform = `translate3d(0, ${y}px, 0)`;
          let inner = el.getElementsByClassName('inner-hook')[0];
          inner.style.webkitTransform = `translate3d(${x}px,0, 0)`;
          inner.style.transform = `translate3d(${x}px,0, 0)`;
        }
      }
    },
    enter(el) {
      let rf = el.offsetHeight;
      this.$nextTick(() => {
        el.style.display = '';
        el.style.webkitTransform = 'translate3d(0, 0, 0)';
        el.style.transform = 'translate3d(0, 0, 0)';
        let inner = el.getElementsByClassName('inner-hook')[0];
        inner.style.webkitTransform = 'translate3d(0, 0, 0)';
        inner.style.transform = 'translate3d(0, 0, 0)';
      });
    },
    afterEnter(el) {
      let ball = this.dropBalls.shift();
      if (ball) {
        ball.show = false;
        el.style.display = 'none';
      }
    },
    toggleList() {
      if (!this.totalCount) {
        return;
      }
      console.log('sdfsdfsdf');
      this.fold = !this.fold;
    }
  }
};
</script>

<style scoped lang="scss">
  @import "../../common/stylus/mixin";
 .shopcart {
   position: fixed;
   left: 0;
   bottom: 0;
   z-index: 50;
   width: 100%;
   height: 48px;
   background: #000;
   .content {
     display: flex;
     background: #141d27;
     font-size: 0;
     .content-left {
       flex: 1;
       .logo-wrapper {
         display: inline-block;
         position: relative;
         top: -10px;
         margin: 0 12px;
         padding: 6px;
         width: 56px;
         height: 56px;
         box-sizing: border-box;
         vertical-align: top;
         border-radius: 50%;
         background: #141d27;
         .logo {
           width: 100%;
           height: 100%;
           border-radius: 50%;
           background: #2b343c;
           text-align: center;
           &.highlight {
             background: rgb(0, 160, 220);
           }
           .icon-shopping_cart {
             line-height: 44px;
             font-size: 24px;
             color: #80858a;
             &.highlight {
               color: #fff;
             }
           }
         }

         .num {
           position: absolute;
           top: 0;
           right: 0;
           width: 24px;
           height: 16px;
           line-height: 16px;
           text-align: center;
           border-radius: 16px;
           font-size: 9px;
           font-weight: 700;
           color: #fff;
           background: rgb(240, 20, 20);
           box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.4);
         }
       }
       .price {
         display: inline-block;
         vertical-align: top;
         margin-top: 12px;
         padding-right: 12px;
         line-height: 24px;
         box-sizing: border-box;
         border-right: 1px solid rgba(255, 255, 255, 0.1);
         font-size: 16px;
         font-weight: 700;
         color: rgba(255, 255, 255, 0.4);
       }
       .desc {
         display: inline-block;
         vertical-align: top;
         line-height: 24px;
         margin: 12px 0 0 12px;
         font-size: 10px;
         color: rgba(255, 255, 255, 0.4);
       }
     }
     .content-right {
       flex: 0 0 105px;
       width: 105px;
       .pay {
         height: 48px;
         line-height: 48px;
         text-align: center;
         font-size: 12px;
         color: rgba(255, 255, 255, 0.4);
         font-weight: 700;
         background: #2b333b;
         &.not-enough {
           background: #2b333b;
         }
         &.enough {
           background: #00b43c;
           color: #fff;
         }
       }
     }
     .ball-container {
       .drop-enter-active, .drop-leave-active {
         transition: opacity .5s
       }
       .drop-enter, .drop-leave-active {
         opacity: 0
       }
       .ball {
         position: fixed;
         left: 32px;
         bottom: 22px;
         z-index: 200;
         //y 轴 贝塞尔曲线
         transition: all 1.2s cubic-bezier(0.49, -0.29, 0.75, 0.41);
         .inner {
           width: 16px;
           height: 16px;
           border-radius: 50%;
           background-color: rgb(0, 160, 220);
           transition: all 1.2s linear;
         }
       }
     }

     .shopcart-list {
       position: absolute;
       left: 0;
       top: 0;
       z-index: -1;
       width: 100%;
       transition: all 0.4s linear;
       // 进入前和离开后的状态（平移）
       transform: translate3d(0, -100%, 0);
       &.fold-enter, &.fold-leave-active {
         opacity: 0;
         transform: translate3d(0, 0, 0);
       }
       .list-header {
         height: 40px;
         line-height: 40px;
         padding: 0 18px;
         background: #f3f5f7;
         border-bottom: 1px solid rgba(7, 17, 27, 0.1);
         .title {
           float: left;
           font-size: 14px;
           color: rgb(7, 17, 27);
         }
         .empty {
           float: right;
           font-size: 14px;
           color: rgb(0, 160, 220);
         }
       }
       .list-content {
         padding: 0 18px;
         max-height: 217px;
         background: #fff;
         overflow: hidden;
         .food {
           position: relative;
           padding: 12px 0;
           box-sizing: border-box;
           @include border-1px(rgba(7, 17, 27, 0.1));
           .name {
             line-height: 24px;
             font-size: 14px;
             color: rgb(7, 17, 27);
           }
           .price {
             position: absolute;
             right: 90px;
             bottom: 12px;
             line-height: 24px;
             font-size: 14px;
             font-weight: 700;
             color: rgb(240, 20, 20);
           }
           .cartcontrol-wrapper {
             position: absolute;
             right: 0;
             bottom: 10px;
           }
         }
       }
     }
   }
 }
</style>
