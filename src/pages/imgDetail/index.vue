<template>
  <view>
    <view class="user_info">
      <view class="user_icon">
        <image :src="imgDetail.user.avatar" mode="widthFix" />
      </view>
      <view class="user_desc">
        <view class="user_name">{{imgDetail.user.name}}</view>
        <view class="user_time">{{imgDetail.cnTime}}</view>
      </view>
    </view>

    <view class="high_img">
      <swiperAction @swiperAction="handleSwiperAction">
        <image :src="imgDetail.thumb" mode="widthFix" />
      </swiperAction>
    </view>

    <view class="user_rank">
      <view class="rank">
        <text>{{imgDetail.rank}}</text>
      </view>
      <view class="user_collect">
        <text>收藏</text>
      </view>
    </view>

    <view class="album_wrap" v-if="album.length">
      <view class="album_title">相关</view>
      <view class="album_list">
        <view class="album_item" v-for="item in album" :key="item.id">
          <view class="album_cover">
            <image :src="item.cover" mode="widthFix" />
          </view>
          <view class="album_info">
            <view class="album_info_text">专辑</view>
            <view class="album_name">{{item.name}}</view>
          </view>
        </view>
      </view>
    </view>

    <view class="comment hot" v-if="hot.length">
      <view class="commment_title">
        <text>最热评论</text>
      </view>
      <view class="comment_list">
        <view class="comment_item" v-for="item in hot" :key="item.id">
          <view class="comment_user">
            <view class="user_icon">
              <image :src="item.user.avatar" mode="widthFix" />
            </view>
            <view class="user_name">
              <view class="user_nickname">{{item.user.name}}</view>
              <view class="user_time">{{item.cnTime}}</view>
            </view>
            <view class="user_badge">
              <image v-for="item2 in item.user.title" :key="item2.icon" :src="item2.icon" mode />
            </view>
          </view>
          <view class="comment_desc">
            <view class="comment_content">{{item.content}}</view>
            <view class="comment_like">
              <text>{{item.size}}</text>
            </view>
          </view>
        </view>
      </view>
    </view>

    <view class="comment new" v-if="comment.length">
      <view class="commment_title">
        <text>最新评论</text>
      </view>
      <view class="comment_list">
        <view class="comment_item" v-for="item in comment" :key="item.id">
          <view class="comment_user">
            <view class="user_icon">
              <image :src="item.user.avatar" mode="widthFix" />
            </view>
            <view class="user_name">
              <view class="user_nickname">{{item.user.name}}</view>
              <view class="user_time">{{item.cnTime}}</view>
            </view>
            <view class="user_badge">
              <image v-for="item2 in item.user.title" :key="item2.icon" :src="item2.icon" mode />
            </view>
          </view>
          <view class="comment_desc">
            <view class="comment_content">{{item.content}}</view>
            <view class="comment_like">
              <text>{{item.size}}</text>
            </view>
          </view>
        </view>
      </view>
    </view>

    <view class="download">
      <view class="download_btn" @click="handleDownload">下载图片</view>
    </view>
  </view>
</template>

<script>
import moment from "moment";
import swiperAction from "@/components/swiperAction";
moment.locale("zh-cn");
export default {
  components: {
    swiperAction
  },
  data() {
    return {
      imgDetail: {},
      album: [],
      hot: [],
      comment: [],
      imgIndex: 0
    };
  },
  onLoad() {
    // console.log(getApp().globalData);
    const { imgIndex } = getApp().globalData;

    this.imgIndex = imgIndex;
    console.log(this.imgIndex);
    this.getData();
  },
  methods: {
    getCommets(id) {
      this.request({
        url: `http://157.122.54.189:9088/image/v2/wallpaper/wallpaper/${id}/comment`
      }).then(res => {
        this.album = res.res.album;
        res.res.hot.forEach(v => (v.cnTime = moment(v.atime * 1000).fromNow()));
        res.res.comment.forEach(
          v => (v.cnTime = moment(v.atime * 1000).fromNow())
        );
        this.hot = res.res.hot;
        this.comment = res.res.comment;
        // console.log(res);
      });
    },
    handleSwiperAction(e) {
      const { imgList } = getApp().globalData;

      if (e.direction === "left" && this.imgIndex < imgList.length - 1) {
        this.imgIndex++;
        this.getData();
      } else if (e.direction === "right" && this.imgIndex > 0) {
        this.imgIndex--;
        this.getData();
      } else {
        uni.showToast({
          title: "没有数据了",
          icon: "none"
        });
      }
    },
    getData() {
      const { imgList } = getApp().globalData;

      this.imgDetail = imgList[this.imgIndex];
      this.imgDetail.cnTime = moment(this.imgDetail.atime * 1000).fromNow();
      this.getCommets(this.imgDetail.id);
    },
    async handleDownload() {
      await uni.showLoading({
        title: '下载中'
      });
      const result1=await uni.downloadFile({url: this.imgDetail.img})
      const {tempFilePath}=result1[1];

      const result2=await uni.saveImageToPhotosAlbum({filePath:tempFilePath});

      uni.hideLoading();
      await uni.showToast({
        title: '下载成功'
      });
    }
  }
};
</script>

<style lang="scss" scoped>
.user_info {
  display: flex;
  padding: 20rpx;
  .user_icon {
    padding: 0 20rpx;
    image {
      width: 88rpx;
      border-radius: 50%;
    }
  }

  .user_desc {
    .user_name {
      color: #000;
      font-weight: 600;
    }

    .user_time {
      color: #ccc;
      font-size: 24rpx;
      padding: 10rpx 0;
    }
  }
}

.user_rank {
  display: flex;
  height: 80rpx;
  border-bottom: 3px solid #eee;
  .rank {
    flex: 1;
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .user_collect {
    flex: 1;
    display: flex;
    justify-content: center;
    align-items: center;
  }
}

.album_wrap {
  padding: 20rpx;
  .album_title {
    padding: 10rpx 0;
  }

  .album_list {
    .album_item {
      display: flex;
      padding: 10rpx 0;
      .album_cover {
        flex: 1;
      }

      .album_info {
        flex: 3;
        padding-left: 20rpx;
        .album_info_text {
          width: 100rpx;
          height: 50rpx;
          background-color: $color;
          color: #fff;
          display: flex;
          justify-content: center;
          align-items: center;
        }

        .album_name {
          padding: 10rpx 0;
          color: #888;
        }
      }
    }
  }
}

.comment {
  .comment_title {
    padding: 15rpx;
    font-weight: 600;
    font-size: 36rpx;
    color: #666;
  }

  .comment_list {
    .comment_item {
      border-bottom: 15rpx solid #eee;
      .comment_user {
        padding: 10rpx 0;
        display: flex;
        .user_icon {
          width: 15%;
          display: flex;
          justify-content: center;
          align-items: center;
          image {
            width: 90%;
          }
        }

        .user_name {
          flex: 1;
          .user_nickname {
            color: #777;
          }

          .user_time {
            color: #ccc;
            font-size: 24rpx;
            padding: 5rpx;
          }
        }

        .user_badge {
          image {
            width: 40rpx;
            height: 40rpx;
            display: inline-block;
          }
        }
      }

      .comment_desc {
        padding: 30rpx 0;
        display: flex;
        .comment_content {
          flex: 1;
          padding-left: 15%;
        }

        .comment_like {
          text-align: right;
          text {
          }
        }
      }
    }
  }
}

.download{
  height: 120rpx;
  display: flex;
  align-items: center;
  justify-content: center;
  .download_btn{
    width: 90%;
    height: 80%;
    background-color: $color;
    color: #fff;
    font-size: 50rpx;
    font-weight: 600;
    display: flex;
    justify-content: center;
    align-items: center;
  }
}
</style>
