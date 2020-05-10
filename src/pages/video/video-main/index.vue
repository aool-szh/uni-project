<template>
  <scroll-view scroll-y enable-flex class="video_main" @scrolltolower="handleScrolltolower">
    <view class="video_item" v-for="item in videowp" :key="item.id" @click="handleGoVideo(item)">
      <image :src="item.img" mode="widthFix" />
    </view>
  </scroll-view>
</template>

<script>
export default {
  data() {
    return {
      videowp: [],
      hasMore: true
    };
  },
  props: {
    urlobj: Object
  },
  watch: {
    urlobj() {
      this.videowp = [];
      this.getList();
    }
  },
  mounted() {
    this.getList();
  },
  methods: {
    getList() {
      this.request({
        url: this.urlobj.url,
        data: this.urlobj.params
      }).then(res => {
        console.log(this.urlobj);
        if (res.res.videowp.length === 0) {
          this.hasMore = false;
          uni.showToast({
            title: "没有跟多数据",
            icon: "none"
          });
        }
        this.videowp = [...this.videowp, ...res.res.videowp];
      });
    },
    handleScrolltolower() {
      if (this.hasMore) {
        this.urlobj.params.skip += this.urlobj.params.limit;
        this.getList();
      } else {
        uni.showToast({
          title: "没有跟多数据了",
          icon: "none"
        });
      }
    },
    handleGoVideo(item) {
      console.log(item);
      getApp().globalData.video= item;
      uni.navigateTo({
         url: "/pages/videoPlay/index"
      });
    }
  }
};
</script>

<style lang="scss" scoped>
.video_main {
  display: flex;
  flex-wrap: wrap;
  height: calc(100vh - 36px);
  .video_item {
    width: 33.33%;
  }
}
</style>
