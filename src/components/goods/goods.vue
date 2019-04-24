<template>
  <div class="goods">
    <div class="menu-wrapper" ref="menuWrapper">
      <ul>
        <li v-for="(item, index) in goods" class="menu-item"
            :class="{'current': currentIndex === index}"
            @click="selectMenu(index)"
        v-bind:key="index">
          <span class="text border-1px">
            <span v-show="item.type > 0" class="icon"
            :class="classMap[item.type]"></span>
            {{item.name}}
          </span>
        </li>
      </ul>
    </div>
    <div class="foods-wrapper" ref="foodWrapper">
      <ul>
        <li v-for="(item, index) in goods"
            :key="index"
        class="food-list food-list-hook">
          <h1 class="title">{{item.name}}</h1>
          <ul>
            <li v-for="(food, index1) in item.foods"
                v-bind:key="index1"
            class="food-item border-1px"
            @click="selectFood(food, $event)">
              <div class="icon">
                <img :src="food.icon" width="57" height="57">
              </div>
              <div class="content">
                <h2 class="name">{{food.name}}</h2>
                <p class="desc">{{food.description}}</p>
                <div class="extra">
                  <span class="count">月售{{food.sellCount}}</span>
                  <span>好评率{{food.rating}}</span>
                </div>
                <div class="price">
                  <span class="now">￥{{food.price}}</span>
                  <span class="old" v-show="food.oldPrice">￥{{food.v}}</span>
                </div>
                <div class="cartcontrol-wrapper">
                  <cart-control :food="food" v-on:cart-add="cartAdd"></cart-control>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <shop-cart
      ref="shopCart"
      :select-foods="selectFoods"
      :delivery-price="seller.deliveryPrice"
      :min-price="seller.minPrice"
    ></shop-cart>
    <food :food="selectedFood"
    ref="food"></food>
  </div>
</template>

<script>
import BScroll from 'better-scroll';
import shopCart from '../shopcart/shopcart';
import cartControl from '../cartcontrol/cartcontrol';
import food from '../food/food';

export default {
  name: 'goods',
  props: {
    seller: {
      type: Object
    }
  },
  components: {
    'shop-cart': shopCart,
    'cart-control': cartControl,
    food
  },
  data() {
    return {
      classMap: ['decrease', 'discount', 'special', 'invoice', 'guarantee'],
      goods: [],
      listHeight: [],
      scrollY: 0,
      selectedFood: {}
    };
  },
  created () {
    this.$http.get('/api/goods')
      .then((response) => {
        const res = response.body;
        this.goods = res.data;
        this.$nextTick(() => {
          this._initScroll();
          this._calculateHeight();
        });
      });
  },
  methods: {
    _initScroll() {
      this.menuScroll = new BScroll(this.$refs.menuWrapper, {
        click: true
      });
      this.foodsScroll = new BScroll(this.$refs.foodWrapper, {
        probeType: 3,
        click: true
      });
      this.foodsScroll.on('scroll', (pos) => {
        this.scrollY = Math.abs(Math.round(pos.y));
      });
    },
    _calculateHeight() {
      let foodList = this.$refs.foodWrapper.getElementsByClassName('food-list-hook');
      let height = 0;
      this.listHeight.push(height);
      for (let i = 0; i < foodList.length; i += 1) {
        const item = foodList[i];
        height += item.clientHeight;
        this.listHeight.push(height);
      }
    },
    selectMenu(index) {
      console.log(index);
      const foodList = this.$refs.foodWrapper.getElementsByClassName('food-list-hook');
      const el = foodList[index];
      this.foodsScroll.scrollToElement(el, 300);
    },
    cartAdd(el) {
      this.$nextTick(() => {
        this.$refs.shopCart.drop(el); // 调用shopcart组件的drop()函数
      });
    },
    selectFood(food, e) {
      this.selectedFood = food;
      this.$refs.food.show();
    }
  },
  computed: {
    currentIndex() {
      for (let i = 0; i < this.listHeight.length - 1; i += 1) {
        const height1 = this.listHeight[i];
        const height2 = this.listHeight[i + 1];
        if (this.scrollY > height1 && this.scrollY < height2) {
          return i;
        }
      }
      return 0;
    },
    selectFoods() {
      let foods = [];
      this.goods.forEach((good) => {
        good.foods.forEach((food) => {
          if (food.count) {
            foods.push(food);
          }
        });
      });
      return foods;
    }
  },
  events: {
    'cart.add'(target) {
      this._drop(target);
    }
  }
};
</script>

<style scoped lang="scss">
  @import "../../common/stylus/mixin";

  .goods {
    display: flex;
    position: absolute;
    width: 100%;
    height: 100%;
    top: 174px;
    bottom: 46px;
    overflow: hidden;
    .menu-wrapper {
      flex: 0 0 80px;
      width: 80px;
      background: #f3f5f7;
      .menu-item {
        display: table;
        height: 54px;
        width: 56px;
        padding: 0 12px;
        line-height: 14px;
        font-size: 12px;
        .icon {
          display: inline-block;
          vertical-align: top;
          width: 12px;
          height: 12px;
          margin-right: 2px;
          background-size: 12px;
          background-repeat: no-repeat;
          &.decrease {
            @include bg-image('decrease_3');
          }
          &.discount {
            @include bg-image('discount_3');
          }
          &.guarantee {
            @include bg-image('guarantee_3');
          }
          &.special {
            @include bg-image('special_3');
          }
          &.invoice {
            @include bg-image('invoice_3');
          }
        }
        .text {
          display: table-cell;
          vertical-align: middle;
          @include border-1px(rgba(7, 12, 27, 0.1));
          font-size: 12px;
        }
      }
      .current {
        position: relative;
        z-index: 10;
        margin-top: -1px;
        background: #fff;
        font-weight: 700;
        .text {
          @include border-none();
        }
      }
    }
    .foods-wrapper {
      flex: 1;
      .title {
        padding-left: 14px;
        height: 26px;
        line-height: 26px;
        border-left: 2px solid #d9dde1;
        font-size: 12px;
        color: rgb(147, 153, 159);
        background: #f3f5f7;
      }
      .food-item {
        display: flex;
        margin: 18px;
        padding-bottom: 18px;
        @include border-1px(rgba(7, 17, 27, 0.1));
        &:last-child {
          @include border-none();
          margin-bottom: 0;
        }
        .icon {
          flex: 0 0 57px;
          margin-right: 10px;
        }
        .content {
          flex: 1;
          .name {
            margin: 2px 0 8px 0;
            height: 14px;
            line-height: 14px;
            font-size: 14px;
            color: rgb(7, 17, 27);
          }
          .desc, .extra {
            font-size: 10px;
            color: rgb(147, 153, 159);
          }
          .desc {
            margin-bottom: 8px;
            line-height: 12px;
          }
          .extra {
            .count {
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
            right: 0;
            bottom: 12px;
          }
        }
      }
    }
  }
</style>
