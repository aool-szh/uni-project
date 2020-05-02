<template>
  <scroll-view class="album_scroll_view" scroll-y @scrolltolower="handleToLower">
    <!-- swiper -->
    <view class="album_swiper">
      <swiper autoplay indicator-dots circular>
        <swiper-item v-for="item in banner" :key="item.id">
          <image :src="item.thumb" />
        </swiper-item>
      </swiper>
    </view>

    <!-- 列表 -->
    <view class="album_list">
      <navigator class="album_item" v-for="item in album" :key="item.id" :url="`/pages/album/index?id=${item.id}`">
        <viwe class="album_img">
          <image :src="item.cover" mode="aspectFill" />
        </viwe>
        <view class="album_info">
          <view class="album_name">{{item.name}}</view>
          <view class="album_dsec">{{item.desc}}</view>
          <view class="album_btn">
            <view class="album_attention">关注</view>
          </view>
        </view>
      </navigator>
    </view>
  </scroll-view>
</template>

<script>
export default {
  data() {
    return {
      params: {
        limit: 30,
        order: "new",
        skip: 0
      },
      banner: [],
      album: [],
      hasMore: true
    };
  },
  mounted() {
    // 修改页面的标题
    uni.setNavigationBarTitle({ title: "专辑" });
    this.getList();
  },
  methods: {
    handleToLower() {
      if(this.hasMore) {
        this.params.skip+=30;
        this.getList();
      }else{
        uni.showToast({
          title: '没有数据了',
          icon: "none"
        });
      }
    },
    getList() {
      this.request({
        url: "http://157.122.54.189:9088/image/v1/wallpaper/album",
        data: this.params
      }).then(result => {
        if(this.banner.length===0){
        this.banner = result.res.banner;
        }

        if(result.res.album.length===0) {
          this.hasMore=false;
          return
        }

        this.album = [...this.album,...result.res.album];
      });
    }
  }
};
</script>

<style lang="scss" scoped>
.album_scroll_view{
  height: calc(100vh - 36px);
}

.album_swiper {
  swiper {
    height: calc(750rpx / 2.3);
    image {
      height: 100%;
    }
  }
}

.album_list {
  padding: 10rpx;
  .album_item {
    padding: 10rpx 0;
    display: flex;
    border-bottom: 1px solid #ccc;
    .album_img {
      flex: 1;
      padding: 10rpx;
      image {
        width: 200rpx;
        height: 200rpx;
      }
    }

    .album_info {
      flex: 2;
      padding: 0 10rpx;
      overflow: hidden;
      .album_name {
        font-size: 30rpx;
        color: #000;
        padding: 10rppx ;
      }

      .album_dsec {
        padding: 10rpx ;
        font-size: 24rpx;

        text-overflow: ellipsis;
        overflow: hidden;
        white-space: nowrap;
      }

      .album_btn {
        padding: 10rpx;
        display: flex;
        justify-content: flex-end;
        .album_attention {
          color: $color;
          border: 1rpx solid $color;
          padding: 10rpx;
        }
      }
    }
  }
}
</style>
