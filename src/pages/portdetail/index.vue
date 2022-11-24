<template>
  <view>
    <view class="big-img-wrapper" @click="showBigImg()">
      <image class="big-img" mode="aspectFill" :src="defautImg.image"></image>
      <text class="loading" v-if="status == 0">正在生成中，预计需要 30s</text>
    </view>
    <view class="my-scroll">
      <scroll-view class="scroll-view" scroll-x="true" scroll-left="120">
        <view
          class="scroll-view-item"
          v-for="(item, index) in imgList"
          :key="index"
          @click="checkImage(item)"
        >
          <image
            class="img-item checked"
            :src="item.image"
            v-if="item.image == defautImg.image && status == 1"
          ></image>
          <image class="img-item" :src="item.image" v-else></image>
        </view>
      </scroll-view>
    </view>
    <view class="info">
      <view class="text">{{ text }}</view>
      <view class="style"><span>风格</span>{{ style }}</view>
      <view class="time">{{ createTime }}</view>
    </view>
    <view class="port-dir" @click="toPortDir">
      <image src="../../static/file-l.png"/>
      <view class="text">
        我的画夹
      </view>
    </view>
    <!-- <view class="table-bar">
      <custom-tab-bar direction="horizontal" :show-icon="true" :selected="0" @onTabItemTap="checkedTabBar"/>
    </view> -->
  </view>
</template>

<script>
export default {
  data() {
    return {
      interval: null,
      defautImg: "",
      imgList: [],
      waiting: "0",
      createTime: "",
      style: "",
      text: "",
      taskId: "",
      status: "0",
      resolution: "",
    };
  },
  onLoad() {
    const eventChannel = this.getOpenerEventChannel();
    eventChannel.on("argParams", (data) => {
      let info = JSON.parse(JSON.stringify(data));

      this.imgList = [...info.imgUrls];
      this.defautImg = this.imgList[0] || {};
      this.waiting = info.waiting;
      this.createTime = info.createTime;
      this.style = info.style;
      this.text = info.text;
      this.taskId = info.taskId;
      this.status = info.status;
      this.resolution = info.resolution;

      if (info.status == 0) {
        this.getData();
      }
    });
  },
  onUnload: function () {
    clearInterval(this.interval);
  },
  methods: {
    getData() {
      this.interval = setInterval(() => {
        let userId = getApp().globalData.userId;
        uni.request({
          url: `https://www.hichencc.com/WeiXinAi/wenXinSearch.do?userId=${userId}`,
          method: "GET",
          success: (res) => {
            console.log("waiting...", res);
            let data = res.data;
            if (data.state == "0x0000" && data.data) {
              if (data.data.status == "1") {
                clearInterval(this.interval);
                this.showPortData(data.data);
              }
            } else {
              clearInterval(this.interval);
            }
          },
          error: (err) => {
            clearInterval(this.interval);
          },
        });
      },5 * 1000);
    },
    showPortData(data) {
      this.imgList = [...data.imgUrls];
      this.defautImg = this.imgList[0] || {};
      this.waiting = data.waiting;
      this.createTime = data.createTime;
      this.style = data.style;
      this.text = data.text;
      this.taskId = data.taskId;
      this.status = data.status;
      this.resolution = data.resolution;
    },
    checkImage(item) {
      this.defautImg = item;
    },
    showBigImg() {
      let urls = [],
        curr = 1;
      this.imgList.map((item, index) => {
        urls.push(item.image);
        if (item.image == this.defautImg.image) {
          curr = index + 1;
        }
      });
      wx.previewImage({
        urls: urls,
        current: curr,
        success: (res) => {},
        fail: (res) => {},
        complete: (res) => {},
      });
    },
    checkedTabBar() {},
    toPortDir() {
      uni.switchTab({
        url: "/pages/portdir/index",
      })
    }
  },
};
</script>

<style scoped>
.big-img-wrapper {
  position: relative;
  margin-left: 28rpx;
  width: 694rpx;
  height: 694rpx;
  margin-bottom: 20rpx;
}
.big-img-wrapper .big-img {
  width: 694rpx;
  height: 694rpx;
  border-radius: 4px;
}
.big-img-wrapper .loading {
  position: absolute;
  top: 330rpx;
  left: 120rpx;
  font-size: 36rpx;
  color: #fff;
}
.my-scroll {
  margin: 0 28rpx;
}
.my-scroll .scroll-view {
  white-space: nowrap;
  width: 100%;
}
.my-scroll .scroll-view .scroll-view-item {
  display: inline-block;
  width: 150rpx;
  height: 150rpx;
  margin-right: 20rpx;
}
.my-scroll .scroll-view .scroll-view-item:last-child {
  margin-right: 0;
}
.my-scroll .scroll-view .img-item {
  width: 150rpx;
  height: 150rpx;
  border-radius: 4px;
}
.my-scroll .scroll-view .checked {
  border: 1px solid #409eff;
}
.info {
  margin: 20rpx 28rpx 0;
}
.info .text {
  font-size: 14px;
  margin-bottom: 10rpx;
}
.info .style {
  font-size: 12px;
  display: inline-block;
  opacity: 0.6;
  margin-right: 20rpx;
}
.info .style span {
  padding: 0 5px;
  background: #333;
  border-radius: 4rpx;
}
.info .time {
  display: inline-block;
  font-size: 12px;
  opacity: 0.6;
}
.port-dir {
  position: absolute;
  bottom: 150rpx;
  right: 100rpx;
  width: 100rpx;
  height: 100rpx;
  border-radius: 50%;
  border: 3px solid #409eff;
  background: #0030fd;
  box-shadow: 1px 2px 3px rgba(0, 0, 0, 0.08);
}
.port-dir image{
  margin-left: 10rpx;
  margin-top: 10rpx;
  width: 80rpx;
  height: 80rpx;
}
.port-dir .text {
  position: absolute;
  bottom: -10rpx;
  width: 160rpx;
  left: -30rpx;
  font-size: 12px;
  border-radius: 10px;
  background-color: rgb(147, 128, 225);
  opacity: 1;
  color: #333;
  text-align: center;
}
</style>