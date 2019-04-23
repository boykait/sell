<template>
  <div id="app">
    <v-header :seller="seller"></v-header>
    <div class="tab border-1px">
      <div class="tab-item">
        <router-link to="/goods">商品</router-link>
      </div>
      <div class="tab-item">
        <router-link to="/ratings">评论</router-link>
      </div>
      <div class="tab-item">
        <router-link to="/seller">商家</router-link>
      </div>
    </div>
    <router-view :seller="seller"></router-view>
  </div>
</template>

<script>
import header from './components/header/header';
const OK = 0;
export default {
  name: 'App',
  components: {
    'v-header': header
  },
  data() {
    return {
      seller: []
    };
  },
  mounted () {
    this.$http.get('/api/seller').then((response) => {
      const res = response.body;
      if (res.errno === OK) {
        this.seller = res.data;
        console.log(this.seller);
      }
    });
  }
};
</script>

<style lang="scss" scoped>
  @import "./common/stylus/mixin";
  #app {
    & .tab{
      display: flex;
      width: 100%;
      height: 40px;
      line-height: 40px;
      @include border-1px(rgba(7, 17, 27, 0.1));
      & .tab-item {
        flex: 1;
        text-align: center;
        & > a {
          display: block;
          text-decoration: none;
          font-size: 14px;
          color: rgb(77, 85, 93);
        }
        & .active {
          color: rgb(240, 20, 20);
        }
      }
    }
  }
</style>
