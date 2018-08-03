<!-- Created by anyc on 2018-07-06. 组件名称 -->
  <template>
  <div class="page-container">
    <div class="page home-page" flex="dir:top box:justify">
      <nav-bar title="领取代金券" />
      <div class="page-content" flex="dir:top box:first">

        <div class="order-list-container">
          <div class="tab-all tabs" key="all">
            <div class="no-goods" flex="dir:top cross:center" v-if="orderList.length == 0">
              <i class="iconfont icon-goods"></i>
              <span>没有相关代金券</span>
            </div>
            <div class="select-couitem" v-for="(item, index) in orderList" :key="index">
              <select-couitem :item="item" :onClick="selectItem.bind(this, item, index)" :active="index == select" :isreceive="true"></select-couitem>
            </div>
          </div>
        </div>
      </div>

      <div class="button-control" flex="dir:top box:justify">
        <input type="tel" placeholder="请输入手机号" maxlength="11" v-model="mobile" style="border:none;text-align:center;height: 2.1rem; line-height: 2.1rem;font-size: 0.75rem;" />
        <div class="next-button" @click="receiveCoupon">
          确定
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
  name: "receivecoupon",
  data() {
    return {
      orderList: [],
      select: -1,
      mobile: "",
      wbproductId: "",
      storeId: "",
      setmealId: "",
      regexc: /^1[3-9]\d{9}$/
    };
  },
  components: {
    NavBar,
    SelectCouitem
  },
  methods: {
    selectItem: function(item, index) {
      if (this.select == index) {
        this.select = -1;
      } else {
        this.select = index;
      }
    },
    receiveCoupon() {
      if (this.select == -1) {
        Toast({
          duration: 1000,
          message: "请选择代金券类型"
        });
        return false;
      }
      if (this.mobile === "") {
        Toast({
          duration: 1000,
          message: "请输入手机号"
        });
        return false;
      }
      if (!this.regexc.test(this.mobile)) {
        Toast({
          duration: 1000,
          message: "手机号格式不正确"
        });
        return false;
      }
      Tool.post(
        "VoucherReceive",
        {
          mobile: this.mobile,
          wbcashcouponId: this.orderList[this.select].wbcId
        },
        data => {
          if (data.code == 200) {
            Toast({
              duration: 1000,
              message: "领取成功，请到账户查看"
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
    orderQueryAll: function() {
      Tool.post("VoucherList", {}, data => {
        if (data.code == 200) {
          this.orderList = data.data;
        } else {
          Toast({
            duration: 1000,
            message: data.msg
          });
        }
      });
    }
  },
  activated: function() {
    this.orderQueryAll();
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
.page {
  height: 100%;
  position: absolute;
  width: 100%;
  .button-control {
    .next-button {
      text-align: center;
      color: #fff;
      background-color: #389cf2;
      height: 2.1rem;
      line-height: 2.1rem;
      font-size: 0.67rem;
    }
  }
  .page-content {
    background-color: #efefef;
    height: 100%;
    overflow: auto;
    position: relative;
    padding-top: 10px;
    .order-list-container {
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
