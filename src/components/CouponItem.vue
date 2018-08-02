<template>
  <div class="order-container" @touchstart="showDeleteButton" @touchend="clearLoop">
    <div class="body">
      <div class="order-info" flex="dir:top box:mean">
        <!-- baseInfo-canuse baseInfo-used 左侧文字颜色 两种 -->
        <div :class="['baseInfo', {'baseInfo-canuse': item.wbcuStatus=='未使用'}, {'baseInfo-used': item.wbcuStatus!='未使用'}]" flex="dir:top">
          <p class="couponType">
            {{(item.wbcLx=="折扣"||item.wbcLx=="线下抵扣")?item.wbcLx:item.wbcName}}
          </p>
          <div class="couponScene" flex="dir:left box:first">
            <div class="scene-l">
              <span class="h2">¥</span>
              <span class="price nowrap-flex">{{(item.wbcLx=="折扣"||item.wbcLx=="线下抵扣")?item.wbcName:item.wbcPrice}}</span>
            </div>
            <!-- scene-r-used 右侧使用之后的颜色 -->
            <div :class="item.wbcuStatus=='未使用'?'scene-r':'scene-r-used'" style="padding-left: 20px;">
              <p class="h3 useCode nowrap-flex" style="font-size:15px;">使用码: {{item.wbcuNumber}}
              </p>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="footer">
      <div :class="['detail',{'detail-canuse':item.wbcuStatus=='未使用'},{'detail-used':item.wbcuStatus!='未使用'}]">
        <div class="detail-btn" flex="dir:right main:justify cross:center" @click="detailShow=!detailShow">

          <div class="time">
            <span class="startTime">{{item.wbcKsyxq}}</span>-
            <span class="endTime">{{item.wbcJsyxq}}</span>
          </div>
          <div flex="dir:left cross:center">
            <span class="h5">详情
            </span>
            <i class="iconfont" :class="detailShow?'icon-up':'icon-down'"></i>
          </div>
        </div>
        <!-- 时间一定会显示 -->

        <transition name="fade">
          <div class="detail-content" v-show="detailShow">
            <span>{{item.wbcSygz}}</span>
          </div>
        </transition>
      </div>
    </div>
    <div :class="['pic', {'pic-used': item.wbcuStatus=='已使用'}, {'pic-overUse': item.wbcuStatus=='已过期'}]" flex="dir:top"></div>
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
    }
  },
  methods: {}
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
      background-position-x: 40%;
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
              font-size: 1rem;
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
