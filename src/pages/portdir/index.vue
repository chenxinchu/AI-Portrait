<template>
  <view>
    <view class="empty-tip" v-if="portList.length==0">
      ———— 你尚未有生成记录,快去创建吧 ————
    </view>
    <view class="port-list">
      <view
        class="port-item"
        v-for="(item, index) in portList"
        :key="index"
        @click="checkItem(item)"
      >
        <image
          class="item-img"
          mode="aspectFill"
          :src="item.imgUrls ? item.imgUrls[0].image : ''"
        ></image>
        <view class="message" v-if="item.status == '0'">
          <view>正在生成中</view> <view>预计需要 30s</view>
        </view>
        <view class="item-text">{{ item.text }}</view>
        <view class="item-time">{{ item.createTime }}</view>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      userId: getApp().globalData.userId,
      portList: [],
      interval: null,
    };
  },
  onShow() {
    // 获取历史数据
    this.getHistoryList();
    // 获取等待中的数据
    
    this.getPortList();
    // let temp = {status : 0}
    // temp.imgUrls = [
    //                 { image: "../../static/defaut-load.jpg" },
    //                 { image: "../../static/defaut-load.jpg" },
    //                 { image: "../../static/defaut-load.jpg" },
    //                 { image: "../../static/defaut-load.jpg" },
    //                 { image: "../../static/defaut-load.jpg" },
    //                 { image: "../../static/defaut-load.jpg" },
    //               ];
    //               this.portList.push(temp);
  },
  onHide() {
    clearInterval(this.interval);
  },
  methods: {
    getHistoryList() {
      let userId = getApp().globalData.userId;
      uni.request({
        url: `https://www.hichencc.com/WeiXinAi/wenXinHistory.do?userId=${userId}`,
        method: "GET",
        success: (res) => {
          console.log("history", res);
          let data = res.data;
          if (data.state == "0x0000" && data.data) {
            this.portList = [...data.data];
          }
          this.getFirstPortList()
        },
      });
    },
    getFirstPortList() {
      let userId = getApp().globalData.userId;
      uni.request({
          url: `https://www.hichencc.com/WeiXinAi/wenXinSearch.do?userId=${userId}`,
          method: "GET",
          success: (res) => {
            console.log("waiting...", res);
            let data = res.data;
            if (data.state == "0x0000" && data.data) {
              if (data.data.status == "1") {
                // this.showPortData(data.data);
              } else {
                let taskId = ''
                if(this.portList.length > 0) {
                  taskId = this.portList[0].taskId
                }
                if (data.data.taskId != taskId) {
                  let temp = data.data;
                  // 假数据
                  temp.imgUrls = [
                    { image: "../../static/defaut-load.jpg" },
                    { image: "../../static/defaut-load.jpg" },
                    { image: "../../static/defaut-load.jpg" },
                    { image: "../../static/defaut-load.jpg" },
                    { image: "../../static/defaut-load.jpg" },
                    { image: "../../static/defaut-load.jpg" },
                  ];
                  this.portList.push(temp);
                }
              }
            }
          },
          error: (err) => {
          },
        });
    },
    getPortList() {
      // this.portList.push({});
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
                // this.showPortData(data.data);
                 this.getHistoryList()
              }
            } else {
              clearInterval(this.interval);
              this.getHistoryList()
            }
          },
          error: (err) => {
            clearInterval(this.interval);
          },
        });
      }, 5 * 1000);
    },
    checkItem(item) {
      if(item.status == '0') {
        clearInterval(this.interval);
      }
      uni.navigateTo({
        url: "/pages/portdetail/index",

        success: function (res) {
          res.eventChannel.emit("argParams", item);
        },
      });
    },
  },
};
</script>

<style scoped>
.empty-tip {
  margin-top: 60rpx;
  text-align: center;
  opacity: 0.6;
}
.port-item {
  margin-left: 28rpx;
  margin-bottom: 28rpx;
  width: 333rpx;
  display: inline-block;
  position: relative;
  /* height: 350rpx; */
}
.port-item .item-img {
  width: 333rpx;
  height: 333rpx;
  border-radius: 4px;
}
.port-item .message {
  position: absolute;
  top: 130rpx;
  width: 100%;
  text-align: center;
  /* left: 95rpx; */

}
.port-item .item-text {
  font-size: 14px;
  height: 18px;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

.port-item .item-time {
  font-size: 12px;
  opacity: 0.6;
  margin-top: 3px;
}
</style>