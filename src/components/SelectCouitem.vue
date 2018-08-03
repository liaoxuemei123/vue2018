<template>
  <div class="order-container">
    <div class="body" v-tap="onClick">
      <div class="order-info" flex="dir:top box:mean" v-if="!isreceive">
        <div :class="['baseInfo', {'baseInfo-canuse': item.isAble=='Y'}, {'baseInfo-used': item.isAble!='Y'}]" flex="dir:top">
          <p class="couponType">
            {{(item.wbcLx=="折扣"||item.wbcLx=="线下抵扣")?item.wbcLx:item.wbcName}}
          </p>
          <div class="couponScene" flex="dir:left box:first">
            <div class="scene-l">
              <span class="h2">¥</span>
              <span class="price nowrap-flex">{{(item.wbcLx=="折扣"||item.wbcLx=="线下抵扣")?item.wbcName:item.wbcPrice}}</span>
            </div>
            <div :class="item.isAble=='Y'?'scene-r':'scene-r-used'" style="padding-left: 20px;">
              <p class="useCode nowrap-flex" style="font-size:15px;">使用码: {{item.wbcuNumber}}
              </p>
            </div>
          </div>
        </div>
        <div class="select" flex="main:right cross:center" v-if="item.isAble=='Y'">
          <i class="iconfont icon-select" v-if="active"></i>
          <i class="iconfont icon-circle active" v-else></i>
        </div>
      </div>
      <div class="order-info" flex="dir:top box:mean" v-else>
        <div class="baseInfo baseInfo-canuse" flex="dir:top">
          <p class="couponType">
            {{(item.wbcLx=="折扣"||item.wbcLx=="线下抵扣")?item.wbcLx:item.wbcName}}
          </p>
          <div class="couponScene" flex="dir:left box:first">
            <div class="scene-l">
              <span class="h2">¥</span>
              <span class="price nowrap-flex">{{(item.wbcLx=="折扣"||item.wbcLx=="线下抵扣")?item.wbcName:item.wbcPrice}}</span>
            </div>
            <div class="scene-r" style="padding-left: 20px;">
              <!-- <p class="useCode nowrap-flex" style="font-size:15px;">使用码: {{item.wbcuNumber}} -->
              </p>
            </div>
          </div>
        </div>
        <div class="select" flex="main:right cross:center">
          <i class="iconfont icon-select" v-if="active" style="margin-top: -15px;margin-right: 13%;"></i>
          <i class="iconfont icon-circle active" v-else style="margin-top: -15px;margin-right: 13%;"></i>
        </div>
      </div>

    </div>
    <div class="footer">
      <div :class="['detail',{'detail-canuse':item.isAble=='Y'},{'detail-used':item.isAble!='Y'}]" v-if="!isreceive">
        <div class="detail-btn" flex="dir:left main:justify cross:center" @click="detailShow=!detailShow">
          <div flex="dir:left cross:center">
            <span class="h5">详情
            </span>
            <i class="iconfont" :class="detailShow?'icon-up':'icon-down'"></i>
          </div>

          <div class="time">
            <span class="startTime">{{item.wbcKsyxq}}</span>-
            <span class="endTime">{{item.wbcJsyxq}}</span>
          </div>
        </div>
        <transition name="fade">
          <div class="detail-content" v-show="detailShow">
            <span v-if="item.isAble=='Y'">{{item.wbcSygz}}</span>
            <span v-if="item.isAble=='N'">不可用原因：{{item.wbcuRemark}}</span>
          </div>
        </transition>
      </div>

      <div class="detail detail-canuse" v-else>
        <div class="detail-btn" flex="dir:left main:justify cross:center" @click="detailShow=!detailShow">
          <div flex="dir:left cross:center">
            <span class="h5">详情
            </span>
            <i class="iconfont" :class="detailShow?'icon-up':'icon-down'"></i>
          </div>

          <div class="time">
            <span class="startTime">{{item.wbcksyxq}}</span>-
            <span class="endTime">{{item.wbcJsyxq}}</span>
          </div>
        </div>
        <transition name="fade">
          <div class="detail-content" v-show="detailShow">
            <span>{{item.wbcSygz}}</span>
          </div>
        </transition>
      </div>
    </div>
  </div>
</template>
<script>
import Tool from "../utils/Tool";
import { Toast } from "mint-ui";
import Bus from "../utils/Bus.js";
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
    onClick: {
      type: Function,
      default: function() {
        return;
      }
    },
    isreceive: {
      type: Boolean,
      default: false
    },
    active: {
      type: Boolean,
      default: false
    }
  },
  filters: {},
  watch: {
    item: {
      handler: function(val) {
        if (val.isAble) {
          this.detailShow = val.isAble == "Y" ? false : true;
        } else {
          this.detailShow = false;
        }
      },
      immediate: true
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
  .body {
    font-size: 0.67rem;
    // padding: 15px 10px 10px 10px;
    background: url("../assets/b-unuse.png") no-repeat center;
    background-size: 100% 100%;
    width: 100%;
    .order-info {
      height: 2.6rem;
      padding: 10px 10px;
      background: url(../assets/line.png) no-repeat center;
      background-position-x: 40%;
      .select {
        .iconfont {
          font-size: 0.8rem;
          color: #fc4c1d;
        }
        .active {
          color: #aaa;
        }
      }
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
          margin-top: 4px;
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
        height: 25px;
        line-height: 25px;
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
