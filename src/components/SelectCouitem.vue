<template>
  <div class="order-container">
    <div class="body" v-tap="onClick">
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
        <!-- 可以用才显示 -->
        <div class="select" flex="main:right cross:center" v-if="item.status==1">
          <i class="iconfont icon-select" v-if="active"></i>
          <i class="iconfont icon-circle active" v-else></i>
        </div>
      </div>

    </div>
    <div class="footer">
      <!-- detail-used -->
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
    active: {
      type: Boolean,
      default: false
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
  .body {
    font-size: 0.67rem;
    // padding: 15px 10px 10px 10px;
    background: url("../assets/b-unuse.png") no-repeat center;
    background-size: 100% 100%;
    width: 100%;
    .order-info {
      height: 2.6rem;
      padding: 10px 10px;
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
