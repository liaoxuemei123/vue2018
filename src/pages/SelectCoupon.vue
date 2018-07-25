<!-- Created by anyc on 2018-07-06. 组件名称 -->
  <template>
  <div class="page-container">
    <div class="page home-page" flex="dir:top box:first">
      <nav-bar title="代金券" />
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
            <div class="after" :style="{'left':( activeTab * 50 ) + '%'}"></div>
          </div>
        </div>
        <div class="order-list-container">
          <transition-group :name="animate">
            <div class="tab-all tabs" key="all" v-show="activeTab == 0">
              <div class="no-goods" flex="dir:top cross:center" v-if="orderList.length == 0">
                <i class="iconfont icon-goods"></i>
                <span>没有相关代金券</span>
              </div>
              <scroller refMark='0'>
                <div class="select-couitem" v-for="(item, index) in orderList" :key="index">
                  <select-couitem :item="item" :onClick="selectItem.bind(this, item, index)" :active="index == select"></select-couitem>
                </div>
              </scroller>
            </div>
            <div class="tab-unpaid tabs" key="unpaid" v-show="activeTab == 1">
              <div class="no-goods" flex="dir:top cross:center" v-if="unpaidList.length == 0">
                <i class="iconfont icon-goods"></i>
                <span>没有相关代金券</span>
              </div>
              <scroller refMark='1'>
                <div class="select-couitem" v-for="(item, index) in unpaidList" :key="index">
                  <select-couitem :item="item"></select-couitem>
                  <!-- 无法点击 -->
                </div>
              </scroller>
            </div>
          </transition-group>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import NavBar from "../components/NavBar";
import SelectCouitem from "../components/SelectCouitem";
import Tool from "../utils/Tool";
import Scroller from "../components/Scroller";
import { Toast } from "mint-ui";
import store from "../model";
import Bus from "../utils/Bus.js";
export default {
  name: "selectcoupon",
  data() {
    return {
      tabs: [{ value: 0, label: "可用" }, { value: 1, label: "不可用" }],
      orderList: [],
      unpaidList: [],
      activeTab: 0,
      animate: "left",
      select: -1,
      mobile: ""
    };
  },
  components: {
    NavBar,
    Scroller,
    SelectCouitem
  },
  watch: {
    activeTab: function(newdata, old) {
      if (newdata > old) {
        this.animate = "left";
      } else {
        this.animate = "right";
      }
      switch (newdata) {
        case 1:
          if (this.unpaidList.length < 1) {
            this.orderQueryUnPaid();
          }
          break;
        case 0:
          if (this.orderList.length < 1) {
            this.orderQueryAll();
          }
          break;
      }
    }
  },
  methods: {
    selectItem: function(item, index) {
      if (this.select == index) {
        this.select = -1;
        Bus.$emit("data", "");
      } else {
        this.select = index;
        Bus.$emit("data", {
          id: this.orderList[this.select].id,
          price: this.orderList[this.select].price
        });
      }
      // Bus.$emit("msg", "我要传给兄弟组件们，你收到没有");
    },
    // 什么时候触发
    backto: function(orderNo) {
      Bus.$emit({ id: this.orderList[this.select].id, price: this.orderList[this.select].price });
      // 传递id和价格
      // 非父子组件通信的一个方式 event bus
      this.$router.back();
    },
    orderQueryAll: function() {
      Tool.post(
        "VoucherQuery",
        {
          wbproductId: "",
          storeId: "",
          setmealId: "",
          mobile: this.mobile,
          isAble: "可用",
          status: "未使用"
        },
        data => {
          if (data.code == 200) {
            this.orderList = data.data;
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
    orderQueryUnPaid: function() {
      Tool.post(
        "VoucherQuery",
        {
          wbproductId: "",
          storeId: "",
          setmealId: "",
          mobile: this.mobile,
          isAble: "不可用",
          status: "未使用"
        },
        data => {
          if (data.code == 200) {
            this.unpaidList = data.data;
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
    resetData: function() {
      this.orderList = [];
      this.unpaidList = [];
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
    this.resetData();
    this.mobile = Tool.getUserInfo("telephone");
    if (this.activeTab == 0) {
      this.orderQueryAll();
    } else if (this.activeTab == 1) {
      this.orderQueryUnPaid();
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
            }
          }
        }
      }
      .tab-container .after {
        display: block;
        width: 50%;
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
        .select-couitem {
          background-color: #fff;
          margin-bottom: 0.5rem;
          margin-left: 10px;
          margin-right: 10px;
          border-top: none;
          border-bottom: none;
          border-radius: 5px;
        }
      }
    }
  }
}
</style>
