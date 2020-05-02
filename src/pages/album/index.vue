<template>
  <div>
    <view class="album_bg">
      <image :src="album.cover" mode="widthFix" />
      <view class="album_info">
        <view class="album_name">{{album.name}}</view>
        <view class="album_btn">关注专辑</view>
      </view>
    </view>

    <view class="albmu_author">
      <view class="album_author_info">
        <image :src="album.user.avatar" mode="widthFix" />
        <view class="author_name">{{album.user.name}}</view>
      </view>
      <view class="album_author_desc">
        <text>{{album.desc}}</text>
      </view>
    </view>

    <view class="album_list">
      <view class="album_item" v-for="item in wallpaper" :key="item.id">
        <image :src="item.thumb + item.rule.replace('$<Height>',360)" mode="widthFix" />
      </view>
    </view>
  </div>
</template>

<script>
export default {
  data() {
    return {
      params: {
        limit: 30,
        order: "new",
        skip: 0,
        first: 1
      },
      id: -1,
      album: {},
      wallpaper: [],
      hasMore: true
    };
  },
  onLoad(options) {
    this.id = options.id;
    this.getList();
  },
  onReachBottom() {
    if (this.hasMore) {
      this.params.first = 0;
      this.params.skip += this.params.limit;
      this.getList();
    } else {
      uni.showToast({
        title: "没有跟多数据了",
        icon: "none"
      });
    }
  },
  methods: {
    getList() {
      this.request({
        url: `http://157.122.54.189:9088/image/v1/wallpaper/album/${this.id}/wallpaper`,
        data: this.params
      }).then(res => {
        if (Object.keys(this.album).length === 0) {
          this.album = res.res.album;
        }
        if (res.res.wallpaper.length === 0) {
          this.hasMore = false;
          uni.showToast({
            title: "没有跟多数据了",
            icon: "none"
          });
          return;
        }
        this.wallpaper = [...this.wallpaper, ...res.res.wallpaper];
      });
    }
  }
};
</script>

<style lang="scss" scoped>
.album_bg {
  position: relative;

  .album_info {
    position: absolute;
    box-sizing: border-box;
    width: 100%;
    left: 0;
    bottom: 0;
    display: flex;
    justify-content: space-between;
    height: 80rpx;
    align-items: center;
    color: #fff;
    padding: 0 15rpx;
    .album_name {
      font-size: 40rpx;
    }

    .album_btn {
      background-color: $color;
      width: 152rpx;
      height: 50rpx;
      display: flex;
      justify-content: center;
      align-items: center;
      border-radius: 10rpx;
    }
  }
}

.albmu_author {
  padding: 10rpx;
  .album_author_info {
    padding: 10rpx;
    display: flex;
    image {
      width: 50rpx;
    }
    .author_name {
      color: #000;
      margin-left: 15rpx;
    }
  }

  .album_author_desc {
  }
}

.album_list {
  display: flex;
  flex-wrap: wrap;
  .album_item {
    width: 33.33%;
    border: 3rpx solid #fff;
    image {
    }
  }
}
</style>
