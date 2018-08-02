
<!-- Created by anyc on 2018-07-06. 组件名称 -->
  <template>
  <div class="page-container">
    <div class="page home-page" flex="dir:top box:first">
      <nav-bar title="领取代金券" />
      <div class="page-content" flex="dir:top ">
        <div class="meal-list">
          <label class="mint-checklist-title">请选择代金券类型</label>
          <div class="meal-item" v-tap.prevent="selectedMeal.bind(this,index)" v-for="(item,index) in options" :key="index" flex="dir:left cross:center main:justify">
            <div class="oil-item">{{item}}</div>
            <i class="iconfont icon-select" v-if="selectTitle == index"></i>
            <i class="iconfont icon-circle active" v-else></i>
          </div>
        </div>
        <div class="meal-list" flex="dir:top">
          <label class="mint-checklist-title">请输入手机号</label>
          <input type="tel" class="receive" placeholder="请输入手机号" v-model="mobile" maxlength="11">
        </div>
        <div class="sure" @click="receiveCoupon" v-show="1==0">确认领取</div>
      </div>
    </div>
    <!-- <transition name="fade">
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
    </transition> -->
  </div>
</template>

<script>
import NavBar from "../components/NavBar";
import Tool from "../utils/Tool";
import { Toast, Checklist } from "mint-ui";
export default {
  name: "receivecoupon",
  data() {
    return {
      mobile: "",
      value: [],
      selectTitle: "",
      options: ["无条件", "折扣", "满减", "线下抵扣"]
      // options: [
      //   {
      //     label: "无条件",
      //     value: "无条件"
      //   },
      //   {
      //     label: "折扣",
      //     value: "折扣"
      //   },
      //   {
      //     label: "满减",
      //     value: "满减"
      //   },
      //   {
      //     label: "线下抵扣",
      //     value: "线下抵扣"
      //   }
      // ]
    };
  },
  components: {
    NavBar
  },
  watch: {},
  methods: {
    selectedMeal: function(index) {
      this.selectTitle = index;
    },
    receiveCoupon() {
      Tool.post(
        "url",
        {
          xxx: "xxx",
          xxx: "1"
        },
        data => {
          if (data.code == 200) {
            // 成功则页面显示
          } else {
            Toast({
              duration: 1000,
              message: data.msg
            });
          }
        }
      );
    }
  },
  activated: function() {}
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
    .meal-list {
      .meal-item {
        background-color: #fff;
        box-sizing: border-box;
        color: inherit;
        min-height: 48px;
        overflow: hidden;
        position: relative;
        text-decoration: none;
        border-bottom: 1px solid #efefef;
        font-size: 15px;
        padding: 0 10px;
        .iconfont {
          color: #ccc;
          margin-right: 0.1rem;
          font-size: 20px;
        }
        .icon-select {
          color: #26a2ff;
          font-size: 20px;
        }
      }
      .receive {
        border: none;
        border-radius: 6px;
        background-color: white;
        padding: 15px;
        font-size: 17px;
        text-align: center;
      }
      .mint-checklist-title {
        font-size: 15px;
        color: black;
      }
    }

    .sure {
      text-align: center;
      border-radius: 6px;
      font-size: 17px;
      background-color: white;
      height: auto;
      padding: 15px;
    }
  }
}
</style>
