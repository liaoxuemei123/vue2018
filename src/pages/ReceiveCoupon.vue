
<!-- Created by anyc on 2018-07-06. 组件名称 -->
  <template>
  <div class="page-container">
    <div class="page home-page" flex="dir:top box:first">
      <nav-bar title="领取代金券" />
      <div class="page-content" flex="dir:top main:center">
        <input type="tel" class="receive" placeholder="请输入手机号领取优惠券" v-model="mobile">
        <div class="sure" @click="receiveCoupon">确认领取</div>
      </div>
    </div>
  </div>
</template>

<script>
import NavBar from "../components/NavBar";
import Tool from "../utils/Tool";
import { Toast } from "mint-ui";
export default {
  name: "receivecoupon",
  data() {
    return {
      mobile: ""
    };
  },
  components: {
    NavBar
  },
  watch: {},
  methods: {
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
    .receive {
      border: none;
      border-radius: 6px;
      background-color: white;
      padding: 15px;
      font-size: 17px;
      text-align: center;
      margin-bottom: 10px;
    }
    .sure {
      width: 100%;
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
