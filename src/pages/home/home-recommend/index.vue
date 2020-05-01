<template>
  <scroll-view
    @scrolltolower="handleToLower"
    class="recommend_view"
    scroll-y
    v-if="recommends.length>0"
  >
    <!-- 推荐开始 -->
    <view class="recommend_warp">
      <view class="recommend_item" v-for="item in recommends" :key="item.id">
        <image mode="widthFix" :src="item.thumb" />
      </view>
    </view>
    <!-- 推荐结束 -->

    <!-- 月份开始 -->
    <view class="monthes_wrap">
      <view class="monthes_title">
        <view class="monthes_title_info">
          <view class="monthes_info">
            <text>{{monthes.DD}}</text>
            {{monthes.MM}} 月
          </view>
          <view class="monthes_text">你负责美丽就好</view>
        </view>
        <view class="monthes_title_more">更多</view>
      </view>
      <view class="monthes_content">
        <view class="monthes_item" v-for="item in monthes.items" :key="item.id">
          <image mode="aspectFill" :src="item.thumb+item.rule.replace('$<Height>', 360)" />
        </view>
      </view>
    </view>
    <!-- 月份结束 -->

    <!-- 热门开始 -->
    <view class="hots_wrap">
      <view class="hots_titile">
        <text>热门</text>
      </view>
      <view class="hots_content">
        <view class="hot_item" v-for="item in hots" :key="item.id">
          <image mode="widthFix" :src="item.thumb" />
        </view>
      </view>
    </view>
    <!-- 热门结束 -->
  </scroll-view>
</template>

<script>
import moment from "moment";
export default {
  data() {
    return {
      recommends: [],
      monthes: {},
      hots: [],
      parmas: {
        limit: 30,
        order: "hot",
        skip: 0
      },
      hasMore: true
    };
  },
  mounted() {
    this.getList();
  },
  methods: {
    handleToLower() {
      if (this.hasMore) {
        this.parmas.skip += this.parmas.limit;
        this.getList();
      } else {
        uni.showToast({
          title: "没有数据了",
          icon: "none"
        });
      }
    },
    getList() {
      this.request({
        url: "http://157.122.54.189:9088/image/v3/homepage/vertical",
        data: this.parmas
      }).then(result => {

        if (result.res.vertical.length ===0) {
          this.hasMore=false;
          return;
        }
        if (this.recommends.length === 0) {
          console.log(result);
          this.recommends = result.res.homepage[1].items;
          this.monthes = result.res.homepage[2];
          this.monthes.MM = moment(this.monthes.stime).format("MM");
          this.monthes.DD = moment(this.monthes.stime).format("DD");
        }
        this.hots = [...this.hots, ...result.res.vertical];
      });
    }
  }
};
</script>

<style lang="scss" scoped>
.recommend_view {
  height: calc(100vh - 36px);
}
.recommend_warp {
  display: flex;
  flex-wrap: wrap;
  .recommend_item {
    box-sizing: border-box;
    width: 50%;
    border: 3rpx solid #fff;
  }
}

.monthes_wrap {
  .monthes_title {
    display: flex;
    justify-content: space-between;
    padding: 20rpx;
    .monthes_title_info {
      color: $color;
      font-size: 30rpx;
      font-weight: 600;
      display: flex;
      .monthes_info {
        text {
          font-size: 36rpx;
        }
      }

      .monthes_text {
        font-size: 34rp;
        color: #666;
        margin-left: 30rpx;
      }
    }

    .monthes_title_more {
      font-size: 24rpx;
      color: $color;
    }
  }

  .monthes_content {
    display: flex;
    flex-wrap: wrap;
    .monthes_item {
      width: 33.33%;
      bottom: 5px solid #fff;
    }
  }
}

.hots_wrap {
  .hots_titile {
    padding: 20rpx;
    text {
      border-left: 5px solid $color;
      padding-left: 20rpx;
      font-size: 34rpx;
      font-weight: 600;
    }
  }

  .hots_content {
    display: flex;
    flex-wrap: wrap;
    .hot_item {
      width: 33.33%;
      border: 5rpx solid #fff;
      box-sizing: border-box;
      image {
      }
    }
  }
}
</style>
