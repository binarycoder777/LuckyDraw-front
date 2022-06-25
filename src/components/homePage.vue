<template>
  <div class="homPage-wrap">
    <div class="luck-draw-wrap">
      <header class="header-wrap">
        <svg
          t="1630142191932"
          class="icon-wrap"
          viewBox="0 0 1024 1024"
          version="1.1"
          xmlns="http://www.w3.org/2000/svg"
          p-id="7506"
          width="28"
          height="28"
        >
          <path
            d="M372.2496 329.9584L208.64 481.4592h-107.52v163.84a64.1792 64.1792 0 0 0 64 64h212.48l107.52 99.5328V289.4592c0-35.2-58.1376-10.1632-112.8704 40.4992z"
            fill="#EE7C3D"
            p-id="7507"
          ></path>
          <path
            d="M572.16 243.9936v533.7856l-138.9056-128.6144-23.552-21.7856H183.04v-232.96H409.6l23.552-21.8112 139.008-128.6144m65.8944-139.9296c-21.0688 0-59.7248 22.5792-96.8448 56.9344l-163.6096 151.5008h-212.48a64.1792 64.1792 0 0 0-64 64v268.8a64.1792 64.1792 0 0 0 64 64h212.48l163.6096 151.4752c37.0944 34.3552 75.776 56.9344 96.8448 56.9344 10.0096 0 16.0256-5.12 16.0256-16.4096v-780.8c0-11.3408-6.0416-16.4352-16.0256-16.4352zM881.92 551.8592h-86.8608a40.96 40.96 0 0 1 0-81.92h86.8608a40.96 40.96 0 0 1 0 81.92zM880.64 921.9328a40.8064 40.8064 0 0 1-28.9792-12.0064l-90.3424-90.3424A40.96 40.96 0 0 1 819.2 761.6512l90.3424 90.3424A40.96 40.96 0 0 1 880.64 921.9328zM795.2384 269.312a40.96 40.96 0 0 1-28.9536-69.9136l85.3248-85.3248a40.96 40.96 0 0 1 57.9328 57.9328L824.32 257.3312a40.96 40.96 0 0 1-29.0816 11.9808z"
            fill="#333333"
            p-id="7508"
          ></path>
        </svg>
        <span>幸运抽奖</span>
      </header>
      <main class="main-wrap">
        <div class="topbar-wrap">
          <div>当前剩余抽奖次数：{{ currentCount }}</div>
          <el-button
            size="small"
            style="
              border-radius: 50px;
              background: rgba(255, 215, 147, 0.3);
              border: none;
            "
            ><span class="span-wrap">已签到</span></el-button
          >
        </div>
        <div class="game-box">
          <div class="game-innerbox">
            <template v-for="(val, idx) of prizeList">
              <div
                v-if="idx === 4"
                class="game-item game-begin"
                :key="idx"
                @click="beginGame"
              >
                <div class="luck-draw">抽奖</div>
              </div>
              <div
                v-else
                :key="idx"
                class="game-item"
                :class="{
                  active: idx === curGameIdx,
                }"
              >
                <div class="image-wrap">
                  <img
                    class="image"
                    :src="require(`../assets/prize${Number(idx + 1)}.png`)"
                    alt=""
                  />
                </div>
                <div class="text">{{ val }}</div>
              </div>
            </template>
          </div>
        </div>
      </main>
    </div>
    <div class="sidebar">
      <h1 class="history-prize-title">我的奖品</h1>
      <template v-for="item in historyPrize">
        <h4 class="history-prize">{{ item }}</h4>
      </template>
    </div>
    <addressForm
      v-show="addressFormShow"
      @addressFormChangeStatus="addressFormChangeStatus"
      :addressFormShow="addressFormShow"
    />
  </div>
</template>

<script>
import axios from "axios";
import addressForm from "./addressForm.vue";
// import addressForm from '../components/addressForm.vue';
export default {
  name: "homePage",
  data() {
    return {
      currentCount: 10, // 当前剩余抽奖次数
      prizeListStatic: [
        "再来一次",
        "随机限量徽章",
        "掘金新款T恤",
        "谢谢参与",
        "",
        "乐高海洋巨轮",
        "掘金限量桌垫",
        "Yoyo抱枕",
        "Switch",
      ], // 静态
      prizeListDynamic: [],
      prizeList: [],
      curGameIdx: 0, // 当前选中商品索引
      luckDrawUse: 1, // 每次消耗抽奖次数
      Order_List: [0, 1, 2, 3,5,6,7,8],
      testNum: 0, // 随机次之后停下来
      historyPrize: [], // 历史奖品
      probabilityArr: [30, 10, 5, 3, 2, 5, 10, 35], // 概率数组, 要求数组和为100，每个数字对应奖品的百分制
      addressFormShow: false,
      // '再来一次'30%, '随机限量徽章'10%, '掘金新款T恤'5%, '乐高海洋巨轮'3%, 'Switch'2%, 'Yoyo抱枕'5%, '掘金限量桌垫'10%, 'Bug'35%
    };
  },
  components: {
    addressForm,
  },
  created() {
    axios
      .get("http://localhost:80/api/queryPrizeList")
      .then((res) => {
        if (res.status === 200) {
          console.log("奖品信息为后端获取");
          this.currentCount = res.data.leftCount;
          this.prizeList = res.data.prizeListDto;
        }
      })
      .catch((err) => {
        console.log(err, "后端接口请求失败");
        this.prizeList = this.prizeListStatic;
        console.log("奖品信息为静态");
      });
  },
  methods: {
    beginGame() {
      axios
        .post("http://localhost:80/api/doDraw", {
          uid: "atao",
          activityId: "100001L",
        })
        .then( (response) => {
          this.currentCount -= this.luckDrawUse;
          this.curGameIdx = 0; //清空当前的奖品
          let index = 0;
          let arr = JSON.parse(JSON.stringify(this.Order_List));
          console.log(response)
          let int = setInterval(() => {
            if (this.prizeList[this.curGameIdx] == response.data.awardDTO.awardName && index > 30) {
              clearInterval(int);
              console.log(this.prizeList[this.curGameIdx])
              console.log(response.data.awardDTO.awardName)
              console.log(this.prizeList[this.curGameIdx] == response.data.awardDTO.awardName)
              // 加入历史中奖记录
              this.historyPrize.push(this.prizeList[this.curGameIdx]);
              // 打开记录填写表单
              this.addressFormChangeStatus();
              // 退出
              return;
            }
            index++;
            if (arr.length) {
              this.curGameIdx = arr.shift();
            } else {
              arr = JSON.parse(JSON.stringify(this.Order_List));
              this.curGameIdx = arr.shift();
            }
          }, 100);
        })
        .catch(function (error) {
          console.log(error);
        });
    },
    addressFormChangeStatus(formInfo) {
      if (formInfo) {
      }
      this.addressFormShow = !this.addressFormShow;
    },
  },
};
</script>


<style lang="scss" scoped>
$wrapWidth: 43vw;
$gameItemWidth: calc(
  (43vw - 30px - 20px - 6px) / 3 - 4px
); /* $wrapWidth/3向下取整 */
.homPage-wrap {
  display: flex;
  justify-content: space-around;
  align-items: center;
  height: 80vh;

  .luck-draw-wrap {
    color: white;
    width: $wrapWidth;
    /*position: absolute;
    left: 50%;
    top: 50%;
    transform: translateX(-50%) translateY(-50%);*/
    display: flex;
    align-items: center;
    flex-direction: column;

    .header-wrap {
      display: flex;
      justify-content: flex-start;
      align-items: baseline;
      width: $wrapWidth;

      .icon-wrap {
      }
    }

    .main-wrap {
      width: $wrapWidth;

      .topbar-wrap {
        margin: 10px 5px;
        display: flex;
        justify-content: space-between;
        align-items: baseline;

        .span-wrap {
          color: #c9cdd4;
          line-height: 20px;
          padding: 0 5px;
          font-size: 16px;
        }
      }

      .game-box {
        color: #af6819;
        background-color: #e17322;
        border: 15px solid #f9d796;
        border-radius: 12px;

        .game-innerbox {
          display: flex;
          flex-wrap: wrap;
          text-align: center;
          border: 10px solid #e17322;

          .game-item {
            display: flex;
            align-items: center;
            flex-wrap: wrap;
            justify-content: center;
            width: $gameItemWidth;
            height: 15vh;
            background: #fcf3f4;
            border-radius: 4px;
            /* border: 2px solid #E17322; */
            margin: 3px;
            transition: all 0.2s;

            .luck-draw {
              font-size: 32px;
              line-height: 42px;
              font-weight: 600;
              color: #a74b00;
              width: 100%;
            }

            &.game-begin {
              background: #ffe6a6;
              box-shadow: inset 0 0 16px #ffa800;
            }

            &.active {
              background-color: #f9cd8c;
            }

            .image-wrap {
              width: $gameItemWidth;

              .image {
                max-width: 50px;
              }
            }
          }
        }
      }
    }
  }

  .sidebar {
    margin-top: 80px;
    width: 20vw;
    height: 400px;
    border-radius: 12px;
    background: rgba(255, 247, 232, 0.3);
    padding: 16px;
    box-sizing: border-box;
    text-align: center;
    overflow: auto;

    .history-prize-title {
      color: #fadd95;
      font-size: 20px;
      line-height: 28px;
    }

    .history-prize {
      color: #fcf3f4;
    }
  }
}
</style>
