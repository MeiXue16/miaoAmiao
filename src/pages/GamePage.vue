<template>
  <div id="gamePage">
    <a-row align="space-between">
      <a-button style="margin-bottom: 8px" @click="doBack"> 返回</a-button>
      <a-button>块数：{{ clearBlockNum }} / {{ totalBlockNum }}</a-button>
    </a-row>
    <!-- 胜利 -->
    <a-row align="center">
      <div v-if="gameStatus === 3" style="text-align: center">
        <h2>恭喜，你通关啦！🎉</h2>
        <img alt="" src="../assets/kunkun.gif" />
        <!-- <my-ad style="margin-top: 16px" /> -->
      </div>
    </a-row>
    <!-- 分层选块 -->
    <a-row align="center">
      <div v-show="gameStatus > 0" class="level-board">
        <div v-for="(block, idx) in levelBlocksVal" :key="idx">
          <div
            v-if="block.status === 0"
            class="block block-1 level-block"
            :class="{
              disabled: !isHolyLight && block.lowerThanBlocks.length > 0,
            }"
            :data-id="block.id"
            :style="{
              zIndex: 100 + block.level,
              left: block.x * widthUnit + 'px',
              top: block.y * heightUnit + 'px',
            }"
            @click="() => doClickBlock(block)"
          >
            {{ block.type }}
          </div>
          <!-- <audio ref="audio" src="../assets/musics/bo.wav"></audio> -->
        </div>
      </div>
    </a-row>
    <!-- 随机选块 -->
    <a-row align="space-between" class="random-board">
      <div
        v-for="(randomBlock, index) in randomBlocksVal"
        :key="index"
        class="random-area"
      >
        <!-- 隐藏 -->
        <div
          v-for="num in Math.max(randomBlock.length - 1, 0)"
          :key="num"
          class="block block-2 disabled"
        >
          <span v-if="canSeeRandom">
            {{ randomBlock[num].type }}
          </span>
        </div>

        <div
          v-if="randomBlock.length > 0"
          :data-id="randomBlock[0].id"
          class="block block-2"
          @click="() => doClickBlock(randomBlock[0], index)"
        >
          {{ randomBlock[0].type }}
        </div>
      </div>
    </a-row>

    <!-- 槽位 -->
    <a-row v-if="slotAreaVal.length > 0" align="center" class="slot-board">
      <div v-for="(slotBlock, index) in slotAreaVal" :key="index" class="block">
        {{ slotBlock?.type }}
      </div>
    </a-row>
    <!-- 技能 -->
    <div class="skill-board">
      <a-space>
        <a-button size="small" @click="doRevert">撤回</a-button>
        <a-button size="small" @click="doRemove">移出</a-button>
        <a-button size="small" @click="doShuffle">洗牌</a-button>
        <a-button size="small" @click="doBroke">破坏</a-button>
        <a-button size="small" @click="doHolyLight">圣光</a-button>
        <a-button size="small" @click="doSeeRandom">透视</a-button>
      </a-space>
    </div>
  </div>
</template>

<script setup lang="ts">
import useGame from "../core/game";
import { onMounted } from "vue";
import { useRouter } from "vue-router";
import MyAd from "../components/MyAd.vue";
// export default {
//   data() {
//     return {
//       bo: require("../assets/musics/bo.wav"), //声音文件
//     };
//   },
//   methods: {
//     play() {
//       this.$refs.audio.play(); //点击触发 点击音效
//     },
//   },
// };

const router = useRouter();

const {
  gameStatus,
  levelBlocksVal,
  randomBlocksVal,
  slotAreaVal,
  widthUnit,
  heightUnit,
  totalBlockNum,
  clearBlockNum,
  isHolyLight,
  canSeeRandom,
  doClickBlock,
  doStart,
  doShuffle,
  doBroke,
  doRemove,
  doRevert,
  doHolyLight,
  doSeeRandom,
} = useGame();

/**
 * 回上一页
 */
const doBack = () => {
  router.back();
};

onMounted(() => {
  doStart();
});
</script>

<style scoped>
.level-board {
  position: relative;
  border-radius: 5px;
  margin: 0 calc(50% - 184px);
  width: 100%;
}

.level-block {
  position: absolute;
  border-radius: 5px;
  margin-left: 0;
  margin-top: 0;
  margin-right: 0;
  margin-bottom: 0;
}

.random-board {
  margin: 0 calc(50% - 154px);
  margin-top: 8px;
  border-radius: 5px;
}

.random-area {
  margin: 0 calc(50% - 134px);
  margin-top: 8px;
  border-radius: 5px;
  align-items: center;

  /* padding: 5px 2px; */
}

.slot-board {
  /* margin-top: 24px; */
  border: 10px solid hsl(196, 51%, 70%);

  margin: 10px calc(50% - 160px);
  width: fit-content;
  border-radius: 10px;
}

.skill-board {
  text-align: center;
  border-radius: 5px;
  padding: 5px 15px;
  /* margin: 20px; */
  /* font: 2em; */
}

.block {
  font-size: 28px;
  width: 42px;
  height: 42px;
  line-height: 42px;
  border: 1px hsl(194, 0%, 30%);
  /* transform: translate(0, -15%) rotate(-4deg); */
  /* transform-origin: center center; */
  box-shadow: 2px 2px 2px 2px hsl(130, 30%, 72%);
  background: hsl(47, 52%, 91%);
  text-align: center;
  vertical-align: top;
  border-radius: 5px;
  display: inline-block;

  /* margin: -30px -30px; */
}

.block-1 {
  border-radius: 4px;
  border: 1px solid #666;
  margin-left: 0;
  margin-top: 0;
  margin-right: 0;
  margin-bottom: 0;
}

.block-2 {
  margin-left: -30px;
  border-radius: 4px;
  border: 1px solid #666;
}

.disabled {
  background: hsl(262, 0%, 70%);
  cursor: not-allowed;
}
</style>
