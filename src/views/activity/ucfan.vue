<template>
  <div class="container" id="contain">
    <div class="title color1" v-if='!isAndroidWebview'>
      <i @click="goBack()"></i>
      <span>返水活动</span>
    </div>
    <div id="nowyear" :style="{'margin-top':isAndroidWebview ? '0rem':'1.92rem'}">
      <div class="intermediately" @click="ljlq"></div>
      <div class="liushui" @click="flowing"></div>
    </div>
    <promptbox @panelShow="panelShow=false" :promptsystem="promptsystem" :successshow="successshow" :promptboxshow="promptboxshow" :panelShow="panelShow" :promptboxtext="promptboxtext" :erreocode="erreocode"></promptbox>
  </div>
</template>
<script>
import promptbox from "../../components/promptbox";
export default {
  name: "activity1",
  mounted() {},
  components: {
    promptbox
  },
  data() {
    return {
      is_gd_ali: is_gd_ali(),
      isAndroidWebview: location.href.indexOf("?") > -1 ? true : false,
      panelShow: false,
      imMoney: sessionStorage.getItem("im_money"),
      title: "",
      isWan: "",
      titleshow: true,
      isGetBonusesone: "",
      erreocode: "",
      promptboxtext: "",
      panelShow: false,
      promptboxshow: true,
      successshow: false,
      promptsystem: ""
    };
  },
  methods: {
    goBack() {
      history.go(-1);
    },
    ljlq() {
      this.isWan = sessionStorage.getItem("im_username");
      if (this.isWan == "游客") {
        this.times = 0;
        this.promptboxtext = "请登录正式会员账号!";
        this.panelShow = true;
        return;
      }
      let params = {};
      if (window.location.href.indexOf("?") != -1) {
        let userOid = window.location.href.split("?")[1].split("=")[1];
        params.oid = userOid;
      } else {
        let userOid = sessionStorage.getItem('im_token');
        params.oid = userOid;
      }
      if (this.isGetBonusesone == 1) {
        this.panelShow = true;
        this.promptboxtext = "今天已经领取过";
        this.promptboxshow = false;
        return;
      }
      this.$http
        .post(
          `${getUrl()}/DepositActivity/getTodaySingleDepositCashBack`,
          JSON.stringify(params)
        )
        .then(res => {
          this.promptboxshow = false;
          if (res.data.msg == 4001) {
            //  1未登陆
            sessionStorage.clear();
            this.panelShow = true;
            this.promptboxtext = "您的账户已失效，请重新登录";
            setTimeout(() => {
              this.panelShow = false;
              this.$router.push({
                path: "/login"
              });
            }, 1000);
          } else if (res.data.msg == 7001) {
            this.panelShow = true;
            this.promptboxtext = res.data.info;
            this.promptboxshow = false;
          } else if (res.data.msg == 2006) {
            this.panelShow = true;
            this.promptboxtext = res.data.info;
            this.promptboxshow = true;
          }
        })
        .catch(err => {
          console.log(err);
        });
    },
    flowing() {
      this.panelShow = true;
      this.promptboxtext = "请您联系客服彩金专员领取";
      this.promptboxshow = true;
    },
  },
  created() {
    this.isWan = sessionStorage.getItem("im_username");
    if (this.isWan == "游客") {
      this.times = 0;
      return;
    }
    let params = {};
    if (window.location.href.indexOf("?") != -1) {
      this.promptboxtextshow = false;
      let userOid = window.location.href.split("?")[1].split("=")[1];
      params.oid = userOid;
    } else {
      this.promptboxtextshow = true;
      let userOid = sessionStorage.getItem('im_token');
      params.oid = userOid;
    }
  }
};
</script>

<style lang="less" rel="stylesheet/less" scoped>
@import "../../assets/less/variable.less";
@zoom: 46.875rem;
.container {
  padding: 0;
  margin: 0;
  width: 100%;
}
.title {
  width: 100%;
  height: 90/46.875rem;
  background: #196fde;
  line-height: 90/46.875rem;
  text-align: center;
  z-index: 4;
  /*position: fixed;*/
  left: 0;
  top: 0;
  > i {
    display: block;
    width: 35/46.875rem;
    height: 50/46.875rem;
    background: url("@{public_img}/images/backing_out.png") no-repeat;
    background-size: 100% 100%;
    position: absolute;
    left: 0.5rem;
    top: 0.4rem;
  }
  > span {
    color: #ffffff;
    font-size: 0.8rem;
  }
}
#nowyear {
  width: 100%;
  height: 1244/46.875rem;
  background: url(/wap/images/uc/fanshui.png) no-repeat;
  background-size: 100% 100%;
  position: relative;
  > .intermediately {
    position: absolute;
    left: 64%;
    margin-left: -7.04rem;
    width: 4.2rem;
    bottom: 8.5rem;
    height: 1.3rem;
    border-radius: 20px !important;
  }
  > .liushui {
    position: absolute;
    left: 95%;
    margin-left: -7.04rem;
    width: 4.2rem;
    bottom: 8.5rem;
    height: 1.3rem;
    border-radius: 20px !important;
  }
}
</style>
