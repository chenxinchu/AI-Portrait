<script>
import "common/global.css";

export default {
  globalData: {
    userId: "",
  },
  onLaunch: function () {
    this.login();
  },
  onLoad: function () {},
  onShow: function () {
    console.log("App Show");
  },
  onHide: function () {
    console.log("App Hide");
  },
  methods: {
    login() {
      wx.login({
        success: (res) => {
          if (res.code) {
            console.log(res);
            this.getUserId(res.code);
          }
        },
      });
    },
    getUserId(code) {

      uni.request({
        url: `https://www.hichencc.com//WeiXinAi/getUserId.do?tempToken=${code}`,
        method: "GET",
        success: (res) => {
			let data = res.data
          if (data.state == '0x0000' && data.data) {
            getApp().globalData.userId =  data.data.userId || '';
          }
        },
      });
    },
  },
};
</script>

<style>
/*每个页面公共css */
</style>
