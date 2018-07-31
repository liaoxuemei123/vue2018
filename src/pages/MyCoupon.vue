<!-- Created by anyc on 2018-07-06. 组件名称 -->
  <template>
  <div class="page-container">
    <div class="page home-page" flex="dir:top box:first">
      <nav-bar title="我的代金券" :rightContent="currentType.name" :onRight="typeShowFun" rightIcon="icon-down" :buttonWidth=0.29 />
      <div class="page-content" flex="dir:top box:first">
        <div class="tab">
          <div class="tab-container">
            <div class="tab-item-container" flex="dir:left box:mean">
              <div class="tab-item" @click="activeTab = index" v-for="(item, index) in tabs" :key="index" flex="dir:left cross:center main:center" :class="{'active':index==activeTab}">
                <span>
                  {{item.label}}
                </span>
              </div>
            </div>
            <div class="after" :style="{'left':( activeTab * 33 ) + '%'}"></div>
          </div>
        </div>
        <div class="order-list-container">
          <transition-group :name="animate">
            <div class="tab-unpaid tabs" key="unpaid" v-show="activeTab == 0">
              <div class="no-goods" flex="dir:top cross:center" v-if="unusedList.length == 0">
                <i class="iconfont icon-goods"></i>
                <span>暂无未使用代金券</span>
              </div>
              <scroller refMark='0'>
                <div class="coupon-item" v-for="(item, index) in unusedList" :key="index">
                  <coupon-item :item="item" :showDeleteButton="showDeleteButton.bind(this, item)" :clearLoop="clearLoop"></coupon-item>
                </div>
              </scroller>
            </div>
            <div class="tab-paid tabs" key="paid" v-show="activeTab == 1">
              <div class="no-goods" flex="dir:top cross:center" v-if="usedList.length == 0">
                <i class="iconfont icon-goods"></i>
                <span>暂无已使用代金券</span>
              </div>
              <scroller refMark='1'>
                <div class="coupon-item" v-for="(item, index) in usedList" :key="index">
                  <coupon-item :item="item"></coupon-item>
                </div>
              </scroller>
            </div>
            <div class="tab-refund tabs" key="refund" v-show="activeTab == 2">
              <div class="no-goods" flex="dir:top cross:center" v-if="overdueList.length == 0">
                <i class="iconfont icon-goods"></i>
                <span>暂无已过期代金券</span>
              </div>
              <scroller refMark='2'>
                <div class="coupon-item" v-for="(item, index) in overdueList" :key="index">
                  <coupon-item :item="item" :showDeleteButton="showDeleteButton.bind(this, item)" :clearLoop="clearLoop">
                  </coupon-item>
                </div>
              </scroller>
            </div>
          </transition-group>
        </div>
      </div>
    </div>
    <transition name="fade">
      <div class="down-list-mask" v-if="typeShow" @click="typeShow=false"></div>
    </transition>
    <transition name="slide-up">
      <div class="down-list" v-show="typeShow">
        <div class="toolbar" flex="dir:left box:mean">
          <div class="cancel" @click="typeShow=false" flex="dir:left cross:center main:left">
            取消
          </div>
          <div class="sure" @click="selectType" flex="dir:left cross:center main:right">
            确定
          </div>
        </div>
        <mt-picker :slots="couponlist" @change="onTypeChange" valueKey="name"></mt-picker>
      </div>
    </transition>
  </div>
</template>

<script>
import NavBar from "../components/NavBar";
import OrderItem from "../components/OrderItem";
import CouponItem from "../components/CouponItem";
import Tool from "../utils/Tool";
import Scroller from "../components/Scroller";
import { Toast, MessageBox } from "mint-ui";
import store from "../model";
export default {
  name: "mycoupon",
  data() {
    return {
      hasToken: false,
      tabs: [
        { value: 0, label: "未使用" },
        { value: 1, label: "已使用" },
        { value: 2, label: "已过期" }
      ],
      mobile: "",
      typeShow: false,
      unusedList: [],
      usedList: [],
      overdueList: [],
      activeTab: 0,
      animate: "left",
      couponlist: [
        {
          values: [
            { index: 0, name: "全部代金券" },
            { index: 1, name: "满减" },
            { index: 2, name: "无条件" },
            { index: 3, name: "折扣" },
            { index: 4, name: "线下折扣" }
          ]
        }
      ],
      currentType: { index: 0, name: "全部代金券" },
      temp: {},
      Loop: null,
      listAll: {}
    };
  },
  components: {
    NavBar,
    Scroller,
    CouponItem
  },
  watch: {
    activeTab: function(newdata, old) {
      if (newdata > old) {
        this.animate = "left";
      } else {
        this.animate = "right";
      }
      switch (newdata) {
        case 0:
          if (this.unusedList.length < 1) {
            this.orderQueryUnused();
          }
          break;
        case 1:
          if (this.usedList.length < 1) {
            this.orderQueryUsed();
          }
          break;
        case 2:
          if (this.overdueList.length < 1) {
            this.orderQueryOverdue();
          }
          break;
      }
    }
  },
  methods: {
    showDeleteButton(item) {
      clearInterval(this.Loop); //再次清空定时器，防止重复注册定时器
      this.Loop = setTimeout(
        function() {
          MessageBox.confirm("确定要删除吗？代金券删除之后无法恢复")
            .then(action => {})
            .catch(err => {});
        }.bind(this),
        500
      );
    },
    clearLoop() {
      clearInterval(this.Loop);
    },
    onTypeChange(picker, values) {
      // 0 1 2
      this.temp = values[0];
    },
    selectType() {
      this.currentType = this.temp;
      if (this.activeTab == 0) {
        this.unusedList = this.filterList("unusedList");
      } else if (this.activeTab == 1) {
        this.usedList = this.filterList("usedList");
      } else if (this.activeTab == 2) {
        this.overdueList = this.filterList("overdueList");
      }
      this.typeShow = false;
    },
    typeShowFun() {
      this.typeShow = !this.typeShow;
    },
    filterList(list) {
      if (this.currentType.index == 0) {
        return this.listAll[list];
      } else return this.listAll[list].filter(e => e.wbcLx == this.currentType.name);
    },
    orderQueryUnused: function() {
      Tool.post(
        "VoucherQuery",
        {
          mobile: this.mobile,
          status: "未使用"
        },
        data => {
          if (data.code == 200) {
            this.listAll.unusedList = data.data;
            this.unusedList = this.filterList("unusedList");
            this.$nextTick(() => {
              this.$children[1].$children[0].mySroller.scrollTo(0, 0);
              this.$children[1].$children[0].mySroller.y = 0;
              this.$children[1].$children[0].scrollerInfo.y = 0;
            });
          } else {
            Toast({
              duration: 1000,
              message: data.msg
            });
          }
        }
      );
    },
    orderQueryUsed: function() {
      Tool.post(
        "VoucherQuery",
        {
          mobile: this.mobile,
          status: "已使用"
        },
        data => {
          if (data.code == 200) {
            this.listAll.usedList = data.data;
            this.usedList = this.filterList("usedList");
            this.$nextTick(() => {
              this.$children[1].$children[1].mySroller.scrollTo(0, 0);
              this.$children[1].$children[1].mySroller.y = 0;
              this.$children[1].$children[1].scrollerInfo.y = 0;
            });
          } else {
            Toast({
              duration: 1000,
              message: data.msg
            });
          }
        }
      );
    },
    orderQueryOverdue: function() {
      Tool.post(
        "VoucherQuery",
        {
          mobile: this.mobile,
          status: "已过期"
        },
        data => {
          if (data.code == 200) {
            this.listAll.overdueList = data.data;
            this.overdueList = this.filterList("overdueList");
            this.$nextTick(() => {
              this.$children[1].$children[2].mySroller.scrollTo(0, 0);
              this.$children[1].$children[2].mySroller.y = 0;
              this.$children[1].$children[2].scrollerInfo.y = 0;
            });
          } else {
            Toast({
              duration: 1000,
              message: data.msg
            });
          }
        }
      );
    },
    resetData: function() {
      this.unusedList = [];
      this.usedList = [];
      this.overdueList = [];
      // this.currentType = { index: 0, name: "全部代金券" };
      this.listAll = {};
    }
  },
  updated: function() {
    if (this.$children[1] && this.$children[1].$children.length > 0) {
      for (var i = 0; i < this.$children[1].$children.length; i++) {
        if (this.$children[1].$children[i].refMark == this.activeTab) {
          this.$children[1].$children[i].mySroller.scrollTo(
            0,
            this.$children[1].$children[i].scrollerInfo.y
          );
        }
      }
    }
  },
  activated: function() {
    if (this.$route.query.token) {
      this.hasToken = true;
    } else {
      this.hasToken = false;
    }
    this.resetData();
    this.mobile = Tool.getUserInfo("telephone");
    if (this.activeTab == 0) {
      this.orderQueryUnused();
    } else if (this.activeTab == 1) {
      this.orderQueryUsed();
    } else if (this.activeTab == 2) {
      this.orderQueryOverdue();
    }
  },
  beforeRouteEnter: (to, from, next) => {
    Tool.routerEnter(to, from, next);
  }
};
</script>

<style lang="less" scoped>
.page-container {
  height: 100%;
  position: absolute;
  width: 100%;
}
.down-list-mask {
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  position: absolute;
  z-index: 1;
}
.down-list {
  z-index: 1;
  position: absolute;
  bottom: 0rem;
  width: 100%;
  background-color: #fff;
  .toolbar {
    height: 1.5rem;
    font-size: 0.67rem;
    color: #00bffe;
    .cancel {
      padding-left: 1.5rem;
    }
    .sure {
      padding-right: 1.5rem;
    }
  }
}
.page {
  height: 100%;
  position: absolute;
  width: 100%;
  .page-content {
    background-color: #efefef;
    height: 100%;
    overflow: auto;
    position: relative;
    .tab {
      background-color: #fff;
      padding: 0rem 1rem;
      border-bottom: 1px solid #ccc;
      margin-bottom: 0.5rem;
      .tab-container {
        height: 1.7rem;
        position: relative;
        .tab-item-container {
          height: 100%;
          .tab-item.active {
            color: #379df2;
          }
          .tab-item {
            span {
              position: relative;
              .unpay-order {
                position: absolute;
                color: #fff;
                background-color: #ed3f14;
                line-height: 1.3em;
                padding: 0 0.3em;
                right: -0.3rem;
                top: -0.3rem;
                border-radius: 1.2em;
                font-size: 0.47rem;
              }
            }
          }
        }
      }
      .tab-container .after {
        display: block;
        width: 33.3%;
        height: 2px;
        background-color: #379df2;
        position: absolute;
        bottom: 0;
        transition: all 0.3s ease;
      }
    }
    .order-list-container {
      overflow: hidden;
      position: relative;
      .tabs {
        position: absolute;
        width: 100%;
        height: 100%;
        .no-goods {
          margin-top: 2rem;
          width: 100%;
          overflow: auto;
          .iconfont {
            font-size: 2.5rem;
            color: #ccc;
          }
          span {
            height: 2rem;
            line-height: 2rem;
            font-size: 0.7rem;
            color: #aaa;
          }
        }
        .coupon-item {
          background-color: #fff;
          margin-bottom: 0.5rem;
          margin-left: 10px;
          margin-right: 10px;
          border-top: none;
          border-bottom: none;
          border-radius: 5px;
        }
        .load-more {
          height: 1.5rem;
          background-color: #fff;
          line-height: 1.5rem;
          .start-load {
            width: 100%;
            text-align: center;
          }
        }
      }
    }
  }
}
</style>
