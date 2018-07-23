<template>
  <div class="order-container" @touchstart="showDeleteButton" @touchend="clearLoop">
    <div class="body">
      <div class="order-info" flex="dir:top box:mean">
        <!-- baseInfo-canuse baseInfo-used 左侧文字颜色 两种 -->
        <div :class="['baseInfo', {'baseInfo-canuse': item.status==1}, {'baseInfo-used': item.status!=1}]" flex="dir:top">
          <p class="couponType">
            {{item.type|typeFilter}}
          </p>
          <div class="couponScene" flex="dir:left box:first">
            <div class="scene-l">
              <span class="h2">¥</span>
              <span class="price nowrap-flex">{{item.price}}</span>
            </div>
            <!-- scene-r-used 右侧使用之后的颜色 -->
            <div :class="item.status==1?'scene-r':'scene-r-used'">
              <p class="h3 useCondition">规则:{{item.range}}</p>
              <!-- <p class="h3 useCondition unvisible">全场通用</p> -->
              <p class="h3 useCode nowrap-flex">使用码: {{item.code}}
              </p>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="footer">
      <div :class="['detail',{'detail-canuse':item.status==1},{'detail-used':item.status!=1}]">
        <div class="detail-btn" flex="dir:left main:justify cross:center" @click="detailShow=!detailShow">
          <div flex="dir:left cross:center">
            <span class="h5">详情
            </span>
            <i class="iconfont" :class="detailShow?'icon-up':'icon-down'"></i>
          </div>

          <div class="time">
            <span class="startTime">{{item.startTime}}</span>-
            <span class="endTime">{{item.endTime}}</span>
          </div>
        </div>
        <!-- 时间一定会显示 -->

        <transition name="fade">
          <div class="detail-content" v-show="detailShow">
            <span>使用规则:{{item.des}}</span>
          </div>
        </transition>
      </div>
    </div>
    <div :class="['pic', {'pic-used': item.status==2}, {'pic-overUse': item.status==3}]" flex="dir:top"></div>
  </div>
</template>
<script>
import Tool from "../utils/Tool";
import { Toast } from "mint-ui";
export default {
  data() {
    return {
      detailShow: false
    };
  },
  props: {
    item: {
      type: Object,
      default: {}
    },
    showDeleteButton: {
      type: Function,
      default: function() {
        return;
      }
    },
    clearLoop: {
      type: Function,
      default: function() {
        return;
      }
    }
  },
  filters: {
    typeFilter: function(val) {
      switch (val) {
        case 0:
          return "线上代金券";
          break;
        case 1:
          return "线下代金券";
          break;
      }
    },
    universalFilter: function(val) {
      if (val == 1) {
        return "全国服务门店使用";
      } else {
        return "指定服务门店使用";
      }
    }
  },
  methods: {
    viewDetail: function(id) {
      this.$router.push({ path: "orderdetail/" + id });
    },
    goEvaluate: function() {
      this.$router.push({ path: "evaluate" });
    },
    goPay: function(orderNo) {
      this.$router.push({ path: "/orderpay/" + orderNo });
    },
    refund: function(orderNo) {
      this.$router.push({ path: "/refund/" + orderNo });
    },
    goReceipt: function(orderNo) {
      // this.$router.push({name:'personreceipt',params:{needReceipt:this.userInfo.needReceipt,selectTitle:this.userInfo.selectTitle}});
      // needReceipt:'1', //1默认不要发票 selectTitle:'0', // 0代表个人

      // this.setReceiptInfo({needReceipt:1,selectTitle:'0',receiver:'',receMobile:'',zip:'',selectAddr:'',addressCont:'',comName:'',receiptCode:''}); //是否要清空
      this.$router.push({
        name: "personreceipt",
        params: { needReceipt: 1, selectTitle: 1, orderNo: orderNo }
      });
    },
    goReceiptPic: function(id) {
      // this.$router.push({name:'showreceipt',params:{id:id}});
      Tool.post("getInvoiceUrl", { id: id }, data => {
        if (data.code == 200) {
          // this.pdfSrc = data.data
          window.open(data.data, "_self", "location=no");
        } else {
          Toast({
            duration: 1000,
            message: data.msg
          });
        }
      });
    }
  }
};
</script>
<style lang="less" scoped>
p {
  margin: 0;
}
.order-container {
  font-size: 0.58rem;
  line-height: 1.5em;
  position: relative;
  .pic {
    width: 75px;
    height: 75px;
    position: absolute;
    top: 10px;
    right: 20px;
  }
  .pic-used {
    background: url("../assets/used.png") no-repeat center;
    background-size: 100% 100%;
  }
  .pic-overUse {
    background: url("../assets/overuse.png") no-repeat center;
    background-size: 100% 100%;
  }
  .body {
    font-size: 0.67rem;
    background: url("../assets/b-unuse.png") no-repeat center;
    background-size: 100% 100%;
    // padding: 15px 10px 10px 10px;
    width: 100%;
    .order-info {
      height: 2.6rem;
      padding: 10px 10px;
      background: url(../assets/line.png) no-repeat center;
      background-position-x: 30%;
      .baseInfo-canuse {
        color: #ff6e04;
      }
      .baseInfo-used {
        color: #999;
      }
      .baseInfo {
        .couponType {
          font-size: 0.6rem;
        }
        .couponScene {
          margin-top: 8px;
          .scene-l {
            min-width: 40%;
            .h2 {
              font-size: 0.6rem;
            }
            .price {
              font-size: 1.5rem;
              height: 35px;
              line-height: 35px;
            }
          }
          .scene-r-used {
            color: #999;
            font-size: 14px;
          }
          .scene-r {
            color: #000;
            font-size: 14px;
          }
        }
      }
    }
  }
  .footer {
    position: relative;
    font-size: 0.5rem;
    color: #4b4b4b;
    .detail {
      font-size: 0.54rem;
      color: #999;
      padding: 0 10px;
      background-color: #fff;
      border-bottom: 5px solid;
      border-radius: 5px;
      .detail-btn {
        height: 30px;
        line-height: 30px;
      }
    }
    .detail-canuse {
      border-bottom: 5px solid #ff6e04;
    }
    .detail-used {
      border-bottom: 5px solid;
    }
  }
}
</style>
