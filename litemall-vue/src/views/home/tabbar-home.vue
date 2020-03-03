<template>
  <div class="tab_home">
    <van-swipe :autoplay="3000" indicator-color="white">
      <van-swipe-item v-for="(banner, index) in banners" :key="index">
        <img :src="banner.url" style="height:230px;width: 100%;" />
      </van-swipe-item>
    </van-swipe>
    <div class="goods-channel">
      <div
        class="item"
        @click="changeTabbar(channel)"
        v-for="(channel, index) in channel"
        :key="index"
      >
        <img :src="channel.iconUrl" background-size="cover" />
        <span>{{ channel.name }}</span>
      </div>
    </div>

    <!--<van-panel title="优惠券" style=" padding-bottom: 10px;">-->
    <!--<div-->
    <!--class="van-coupon-item"-->
    <!--v-for="(coupon, index) in shopInfos.couponList"-->
    <!--:key="index"-->
    <!--@click="getCoupon(coupon.id)"-->
    <!--&gt;-->
    <!--<div class="van-coupon-item__content">-->
    <!--<div class="van-coupon-item__head">-->
    <!--<h2>-->
    <!--<span>¥</span>-->
    <!--{{ coupon.discount }} 元-->
    <!--</h2>-->
    <!--<p>{{ coupon.desc }} - {{ coupon.tag }}</p>-->
    <!--</div>-->
    <!--<div class="van-coupon-item__body">-->
    <!--<h2>{{ coupon.name }}</h2>-->
    <!--<p>有效期：{{ coupon.days }} 天</p>-->
    <!--</div>-->
    <!--</div>-->
    <!--</div>-->
    <!--</van-panel>-->

    <van-panel>
      <van-grid clickable :column-num="2">
        <van-grid-item
          v-for="(topic, index) in shopInfos.topicList"
          :key="index"
          :url="goTopic(topic.id)"
        >
          <img :src="topic.picUrl" style="width: 90%; max-height: 150px;" />
          <div style="font-size:14px;color:#ab956d;">{{ topic.title }}</div>
          <div style="font-size:10px;color:#ab956d;">{{ topic.subtitle }}</div>
        </van-grid-item>
      </van-grid>
      <div slot="header">
        <van-cell-group>
          <van-cell title="专题精选" isLink>
            <router-link to="/items/topic-list" class="text-desc"
              >更多专题精选</router-link
            >
          </van-cell>
        </van-cell-group>
      </div>
    </van-panel>
  </div>
</template>

<script>
import { getHome, goodsCategory, couponReceive } from '@/api/api';
import scrollFixed from '@/mixin/scroll-fixed';
import _ from 'lodash';

import {
  List,
  Swipe,
  SwipeItem,
  Tabbar,
  TabbarItem,
  Search,
  Panel,
  CouponCell,
  CouponList,
  Toast,
  Card,
  Grid,
  GridItem,
  Row,
  Col,
  Tag
} from 'vant';

export default {
  mixins: [scrollFixed],

  data() {
    return {
      shopInfos: [],
      isLoading: false,
      banners: [
        { url: './images/img_01.png' },
        { url: './images/img_02.png' },
        { url: './images/img_05.png' },
        { url: './images/img_06.png' },
        { url: './images/img_08.png' },
        { url: './images/img_09.png' },
        { url: './images/img_11.png' },
        { url: './images/img_12.png' },
        { url: './images/img_14.png' },
        { url: './images/img_15.png' }
      ],
      channel: [
        { iconUrl: './images/sm01.png', name: '寺院介绍' },
        { iconUrl: './images/sm02.png', name: '诸佛菩萨' },
        { iconUrl: './images/sm03.png', name: '佛教知识' },
        { iconUrl: './images/sm04.png', name: '佛音梵呗' },
        { iconUrl: './images/sm05.png', name: '寺院动态' },
        { iconUrl: './images/sm06.png', name: '牌位供奉' },
        { iconUrl: './images/sm07.png', name: '佛前供灯' },
        { iconUrl: './images/sm08.png', name: '法务流通' }
      ]
    };
  },

  created() {
    this.initViews();
  },

  methods: {
    goDetail(id) {
      return `#/items/detail/${id}`;
    },
    goBrand(id) {
      return `#/items/brand/${id}`;
    },
    goTopic(id) {
      return `#/items/topic/${id}`;
    },
    getCoupon(id) {
      couponReceive({ couponId: id }).then(res => {
        Toast.success('领取成功');
      });
    },
    changeTabbar(o) {
      goodsCategory({ id: o.id }).then(res => {
        let categoryId = res.data.data.currentCategory.id;
        this.$router.replace({
          name: 'category',
          query: { itemClass: categoryId }
        });
      });
    },
    initViews() {
      getHome().then(res => {
        this.shopInfos = res.data.data;
        console.log(this.shopInfos);
      });
    }
  },

  components: {
    [Row.name]: Row,
    [Col.name]: Col,
    [Card.name]: Card,
    [Toast.name]: Toast,
    [CouponCell.name]: CouponCell,
    [CouponList.name]: CouponList,
    [Search.name]: Search,
    [Panel.name]: Panel,
    [List.name]: List,
    [Swipe.name]: Swipe,
    [SwipeItem.name]: SwipeItem,
    [Tabbar.name]: Tabbar,
    [TabbarItem.name]: TabbarItem,
    [Tag.name]: Tag,
    [Grid.name]: Grid,
    [GridItem.name]: GridItem
  }
};
</script>

<style lang="scss" scoped>
.interval_bot {
  margin-bottom: 10px;
}
.van-panel {
  margin-top: 20px;
}
.goods-channel {
  background: #fff;
  display: flex;
  align-items: center;
  flex-wrap: wrap;
  padding-bottom: 0px;
  padding-top: 10px;
}

.goods-channel .item {
  width: 75px;
  height: 75px;
  margin-left: 15px;
}

.goods-channel img {
  display: block;
  width: 40px;
  height: 40px;
  margin: 0 auto;
}

.goods-channel span {
  display: block;
  font-size: 14px;
  text-align: center;
  margin-top: 5px;
  margin-bottom: 10px;
  color: #333;
}
.van-coupon-cell--selected {
  color: #323233;
}
.van-coupon-list {
  height: 100%;
  position: relative;
  background-color: #f8f8f8;
}
.van-coupon-list__field {
  padding: 7px 15px;
}
.van-coupon-list__exchange {
  height: 32px;
  line-height: 30px;
}
.van-coupon-list__list {
  overflow-y: auto;
  padding: 15px 0;
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
  -webkit-overflow-scrolling: touch;
}
.van-coupon-list__close {
  left: 0;
  bottom: 0;
  position: absolute;
  font-weight: 500;
}
.van-coupon-list__empty {
  padding-top: 100px;
  text-align: center;
}
.van-coupon-list__empty p {
  color: #969799;
  margin: 15px 0;
  font-size: 14px;
  line-height: 20px;
}
.van-coupon-list__empty img {
  width: 80px;
  height: 84px;
}
.van-coupon-item {
  overflow: hidden;
  border-radius: 4px;
  margin: 0 15px 15px;
  background-color: #fff;
  -webkit-box-shadow: 0 0 4px rgba(0, 0, 0, 0.1);
  box-shadow: 0 0 4px rgba(0, 0, 0, 0.1);
}
.van-coupon-item:active {
  background-color: #e8e8e8;
}
.van-coupon-item__content {
  display: -webkit-box;
  display: -ms-flexbox;
  display: flex;
  height: 100px;
  padding: 24px 0 0 15px;
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
  border: 1px solid red;
}
.van-coupon-item h2,
.van-coupon-item p {
  margin: 0;
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
}
.van-coupon-item h2 {
  height: 34px;
  font-weight: 500;
  line-height: 34px;
}
.van-coupon-item p {
  font-size: 12px;
  line-height: 16px;
  color: #969799;
}
.van-coupon-item__head {
  min-width: 90px;
}
.van-coupon-item__head h2 {
  color: #f44;
  font-size: 24px;
}
.van-coupon-item__head h2 span {
  font-size: 50%;
}
.van-coupon-item__body {
  -webkit-box-flex: 1;
  -ms-flex: 1;
  flex: 1;
  position: relative;
  border-radius: 0 4px 4px 0;
}
.van-coupon-item__body h2 {
  font-size: 16px;
}
.van-coupon-item__corner {
  top: 16px;
  right: 15px;
  position: absolute;
}
.van-coupon-item__corner .van-icon {
  border-color: #f44;
  background-color: #f44;
}
.van-coupon-item__reason {
  padding: 7px 15px;
  border-top: 1px dashed #ebedf0;
  background-color: #fafafa;
}
.van-coupon-item--disabled:active {
  background-color: #fff;
}
.van-coupon-item--disabled .van-coupon-item__content {
  height: 90px;
}
.van-coupon-item--disabled h2,
.van-coupon-item--disabled p,
.van-coupon-item--disabled span {
  color: #969799;
}
</style>
