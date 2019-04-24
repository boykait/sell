<template>
  <div class="cartcontrol">
    <transition name="move">
      <div class="cart-decrease " v-show="food.count > 0"
           @click="decreaseCart">
        <span class="icon-remove_circle_outline inner"></span>
      </div>
    </transition>
    <div class="cart-count"  v-show="food.count > 0">{{food.count}}</div>
    <div class="cart-add icon-add_circle" @click="addCart"></div>
  </div>
</template>

<script>
export default {
  name: 'cartcontrol',
  props: {
    food: {
      type: Object
    }
  },
  created () {

  },
  methods: {
    decreaseCart() {
      if (this.food.count) {
        this.food.count -= 1;
      }
      console.log('desc');
      // event.stopPropagation(); 父级div有点击事件，需要停止冒泡
      event.stopPropagation();
    },
    addCart() {
      // 设置延时，以保证平抛小球到购车车后购物车的数量再加1
      setTimeout(() => {
        if (!this.food.count) {
          this.$set(this.food, 'count', 1);
        } else {
          this.food.count += 1;
        }
      }, 1200);
      // event.stopPropagation(); 父级div有点击事件，需要停止冒泡
      // 为什么用event.preventDefault()不生效？？
      event.stopPropagation();
      this.$emit('cart-add', event.target);
    }
  }
};
</script>

<style scoped lang="scss">
  .cartcontrol {
    font-size: 0;
    .cart-decrease {
      display: inline-block;
      padding: 6px;
      // 所有属性都将获得过渡效果，时间为0.4s，直线
      transition: all 0.4s linear;
      // 进入前和离开后的状态（平移）
      transform: translate3d(0, 0, 0);
      opacity: 1;
      font-size: 0;
      // 进入前和离开后的状态（旋转）
      .inner {
        text-align: center;
        font-size: 24px;
        line-height: 24px;
        color: rgb(0, 160, 220);
        transform: rotate(180deg);
      }
      // 在进入后和离开前的状态（平移）
      &.move-enter, &.move-leave-active {
        opacity: 0;
        transform: translate3d(24px, 0, 0);
        // 在进入后和离开前的状态（旋转）
        .inner {
          transform: rotate(180deg);
        }
      }
    }
    .cart-count {
      display: inline-block;
      vertical-align: top;
      width: 12px;
      padding-top: 6px;
      line-height: 24px;
      font-size: 10px;
      color: rgb(147, 153, 159);
    }
    .cart-add {
      display: inline-block;
      padding: 6px;
      font-size: 24px;
      line-height: 24px;
      color: rgb(0, 160, 220);
    }
  }
</style>
