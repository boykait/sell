<template>
  <div class="ratingselect">
    <div class="rating-type border-1px">
      <span class="block positive" :class="{'active': selectType === 2}"
      @click="select(2)">
        {{desc.all}}
        <span class="count">{{ratings.length}}</span>
      </span>
      <span class="block positive" :class="{'active': selectType === 0}"
            @click="select(0)">
        {{desc.positive}}
        <span class="count">{{positives.length}}</span>
      </span>
      <span class="block negative" :class="{'active': selectType === 1}"
            @click="select(1)">
        {{desc.negative}}
        <span class="count">{{negatives.length}}</span>
      </span>
    </div>
    <div class="switch" :class="{'on': onlyContent}"
      @click="toggleContent">
      <span class="icon-check_circle">
      </span>
      <span class="text">只看有内容的评价</span>
    </div>
  </div>
</template>

<script>
const POSITIVE = 0;
const NEGATIVE = 1;
const ALL = 2;
export default {
  name: 'ratingselect',
  props: {
    ratings: {
      type: Array,
      default: () => []
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
      default: () => (
        {
          all: '全部',
          positive: '满意',
          negative: '不满意'
        }
      )
    }
  },
  methods: {
    select(type) {
      this.selectType = type;
      this.$emit('ratingType-select', type);
    },
    toggleContent() {
      this.onlyContent = !this.onlyContent;
      this.$emit('ratingType-toggleContent', type);
    }
  },
  computed: {
    positives() {
      return this.ratings.filter(r => r.rateType === POSITIVE);
    },
    negatives() {
      return this.ratings.filter(r => r.rateType === NEGATIVE);
    }
  }
};
</script>

<style scoped lang="scss">
  @import "../../common/stylus/mixin";
  .ratingselect {
    .rating-type {
      padding: 18px 0;
      margin: 0 18px;
      @include border-1px(rgba(9, 17, 27, 0.1));
      .block {
        display: inline-block;
        padding: 8px 12px;
        margin-right: 8px;
        line-height: 16px;
        border-radius: 1px;
        font-size: 12px;
        color: rgb(77, 85, 93);
        &.active {
          color: #fff;
        }
        &.positive {
          background-color: rgba(0 ,160, 220, 0.2);
          &.active {
            background: rgb(0, 160, 220);
          }
        }
        &.negative {
          background-color: rgba(77, 85, 93, 0.2);
          &.active {
            background: rgb(77, 85, 93);
          }
        }
        .count {
          margin-left: 2px;
          font-size: 8px;
        }
      }
    }
    .switch {
      padding: 12px 18px;
      line-height: 24px;
      border-bottom: 1px solid rgba(7, 17, 27, 0.1);
      font-size: 0;
      color: rgb(147, 153, 159);
      .icon-check_circle {
        margin-right: 4px;
        font-size: 24px;
      }
      .text {
        display: inline-block;
        vertical-align: top;
        font-size: 12px;
      }
      &.on {
        .icon-check_circle {
          color: #00c850;
        }
      }
      &.off {

      }
    }

  }
</style>
