<template>
  <view class="content">
	<view class="logo">
		<image src="../../static/logo.png"></image>
	</view>
    <view class="my-textarea">
      <textarea v-model="portFrom.text" class="textarea-item" :placeholder="tagList[3]" />
    </view>

	<view class="tag-title">热门关键词</view>
	<view class="tagList" v-for="(item, index) in tagList" :key="index" @click="chageTagItem(item)">
    <view :class="item == portFrom.text ? 'tagItem checked' : 'tagItem'">
      <span>{{item}}</span>
    </view>
	</view>

	<view class="style-title">风格</view>
	<view class="my-style">
    <scroll-view class="scroll-view" scroll-x="true">
      <view class="top">
        <template v-for="(item, index) in imgList">
          <view class="style-item scroll-view-item" :key="index" v-if="index % 2 == 0">
            <view :class="portFrom.style == item.name ? 'select' : ''" @click="selectStyle(item)">
              <image class="style-img" :src="item.img" alt="">
              <view class="img-text ">{{item.name}}</view>
            </view>
          </view>
        </template>
      </view>
      <view class="bottom">
        <template v-for="(item, index) in imgList" >
          <view class="style-item scroll-view-item" :key="index" v-if="index % 2 == 1">
            <view :class="portFrom.style == item.name ? 'select' : ''" @click="selectStyle(item)">
              <image class="style-img" :src="item.img" alt="">
              <view class="img-text ">{{item.name}}</view>
            </view>
          </view>
        </template>
      </view>
    </scroll-view>
	</view>
	<view class="style-title">尺寸: 1024*1024</view>
	<!-- <view class="resolution">
		<view class="resolution-item">
			<view class="out">
				<view class="in"></view>
			</view>
			<view class="resolution-text">
				{{portFrom.resolution}}
			</view>
		</view>
	</view> -->
	<view class="bottom1"></view>
	<view class="submit">
    	<button class="sub-btn" @click="onSubmit">开始生成</button>
	</view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      userId: "",
      portFrom: {
        text: "",
        style: "二次元",
        resolution: "1024*1024",
      },
      imgList: [
        { img: "../../static/styleicon/ecy.jpg", name: "二次元" },
        // { img: "../../static/styleicon/不限定.jpg", name: "" },
        { img: "../../static/styleicon/fsh.jpg", name: "浮世绘" },
        { img: "../../static/styleicon/gf.jpg", name: "古风" },
        // { img: "../../static/styleicon/写实.jpg", name: "写实风" },
        { img: "../../static/styleicon/low-poly.jpg", name: "low poly" },
        // { img: "../../static/styleicon/未来.jpg", name: "未来" },
        // { img: "../../static/styleicon/像素.jpg", name: "像素风" },
        // { img: "../../static/styleicon/概念.jpg", name: "概念艺术" },
        { img: "../../static/styleicon/sch.jpg", name: "水彩画" },
        { img: "../../static/styleicon/sbpk.jpg", name: "赛博朋克" },
        // { img: "../../static/styleicon/llt.jpg", name: "洛丽塔风" },
        // { img: "../../static/styleicon/blk.jpg", name: "巴洛克风" },
        // { img: "../../static/styleicon/cxs.jpg", name: "超现实主义" },
        { img: "../../static/styleicon/yh.jpg", name: "油画" },
        { img: "../../static/styleicon/kth.jpg", name: "卡通画" },
      ],
      tagList: [
        "穿着时尚潮牌的一只大熊猫，太阳",
        "海滩，落日，棕榈树",
        "短发，精致面容，少年",
        "一只带着酷酷眼镜的蒸汽朋克猫",
      ],
    };
  },
  onLoad() {
    this.getUserId();
  },
  methods: {
    getUserId() {
      // wx.getUserInfo({
      //   success: (res) => {
      //     console.log(res, 1111);
      //     if (res.code) {
      //       this.userId == res.code;
      //     }
      //   },
      // });
    },
    selectStyle(item) {
      this.portFrom.style = item.name;
    },
    onSubmit() {
      uni.showLoading({
        title: "生成中"
      })
      this.userId = getApp().globalData.userId;
      // let params = this.portFrom;
      // params.userId = this.userId;
      // console.log(params);
      this.portFrom.text =
        this.portFrom.text == "" ? this.tagList[3] : this.portFrom.text;

      uni.request({
        url: `https://www.hichencc.com/WeiXinAi/wenXinCreate.do?userId=${this.userId}&text=${this.portFrom.text}&style=${this.portFrom.style}&resolution=${this.portFrom.resolution}`,
        method: "GET",
        success: (res) => {
          uni.hideLoading()
          console.log("create", res);
          let data = res.data;
          if (data.state == "0x0000") {
            // this.toPortDetail(0);
            this.toPortDir()
          } else {
            uni.showToast({
              title: data.message,
              icon: "error",
              duration: 1000
            })
          }
        },
        error: (err) => {
          uni.hideLoading()
        }
      });
    },
    toPortDir() {
      console.log(111);
      uni.switchTab({
        url: "/pages/portdir/index",
      })
    },
    // toPortDetail(status) {
    //   let item = {
    //     imgUrls: [
    //       {image: '../../static/defaut-load.jpg'},
    //       {image: '../../static/defaut-load.jpg'},
    //       {image: '../../static/defaut-load.jpg'},
    //       {image: '../../static/defaut-load.jpg'},
    //       {image: '../../static/defaut-load.jpg'},
    //     ],
    //     waiting: "0",
    //     createTime: new Date(),
    //     style: this.portFrom.style,
    //     text: this.portFrom.text,
    //     taskId: "",
    //     status: status,
    //     resolution: "1024*1024",
    //   };
    //   uni.navigateTo({
    //     url: "/pages/portdetail/index",
    //     success: function (res) {
    //       res.eventChannel.emit("argParams", item);
    //     },
    //   });
    // },
    chageTagItem(item) {
      this.portFrom.text = item;
    },
  },
};
</script>

<style scoped>
/* .content {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
} */

.logo {
  width: 150rpx;
  height: 150rpx;
  margin-left: 300rpx;
  margin-bottom: 60rpx;
}
.logo image {
  width: 150rpx;
  height: 150rpx;
}

.my-textarea {
  margin-left: 28rpx;
  margin-top: 50rpx;
  width: 650rpx;
  height: 60px;
  padding: 10px;
  background: #333;
  border-radius: 4px;
  color: #fff;
}
.textarea-item {
  width: 100%;
  height: 60px;
}
.tag-title {
  margin-left: 28rpx;
  margin-top: 28rpx;
  font-size: 12px;
  opacity: 0.6;
}
.tagList {
  display: inline-block;
}
.tagItem {
  margin-left: 30rpx;
  margin-top: 10rpx;
  margin-bottom: 10rpx;
  max-width: 350rpx;
  height: 20px;
  padding: 0 10rpx;
  overflow: hidden;
  background: #333;
  border-radius: 6px;
  font-size: 12px;
  line-height: 20px;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  /* opacity: 0.6; */
}
.tagList .checked {
  background: #1c2438;
  border: 1px solid #409eff;
}

.style-title {
  margin-left: 28rpx;
  margin-top: 28rpx;
}

.my-style {
  display: flex;
  flex-wrap: wrap;
  /* width: 750rpx; */
  /* margin-top: 18rpx; */
  margin: 18rpx 28rpx 0;
}
.my-style .scroll-view {
  white-space: nowrap;
  width: 100%;
}
.my-style .scroll-view .scroll-view-item {
    display: inline-block;
}
.my-style .top .style-item:last-child,
.my-style .bottom .style-item:last-child{
  /* margin-right: 0rpx; */
  width: 136rpx;
}
.style-item {
  width: 183rpx;
  height: 200rpx;
  /* margin-right: 5%; */
}
.style-img {
  width: 140rpx;
  height: 140rpx;
  /* margin-right: 28rpx; */
  border-radius: 4px;
  /* width: 100%; */
  /* height: 100%; */
}
.img-text {
  width: 140rpx;
  text-align: center;
  font-size: 12px;
}
.select .style-img {
  border: 1px solid #409eff;
  background-color: #000;
}
.select .img-text {
  color: #409eff;
}
.resolution {
  margin: 28rpx 0 0 28rpx;
}
.resolution-item {
  width: 100rpx;
  height: 100rpx;
  margin-right: 20rpx;
}
.resolution-item .out {
  margin-left: 5rpx;
  margin-bottom: 5rpx;
  width: 100rpx;
  height: 100rpx;
  border: 1px solid #409eff;
  border-radius: 4px;
}
.resolution-item .in {
  width: 80rpx;
  height: 80rpx;
  background: #333;
  border-radius: 4px;
  margin: 10rpx;
}
.resolution-item .resolution-text {
  font-size: 12px;
}
.submit {
  position: fixed;
  bottom: 28rpx;
  left: 28rpx;
}
.bottom1 {
  height: 80px;
}
.sub-btn {
  width: 694rpx;
  /* margin-left: 28rpx;
  margin-bottom: 50px; */
  background: #409eff;
  color: #fff;
}
</style>
