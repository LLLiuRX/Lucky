<template>
  <div class="luck">
    <div class="lucky_box">
      <div class="lucky_bg" v-for="(item, index) in list" :key="index">
        <div class="lucky_item" :class="{'play_active': activeIndex === item.id, 'luck_animation': animationFlag}">
          <img src="../assets/pig.png" alt="" class="pig">
        </div>
        <div class="prize" v-show="animationFlag">
          <img :src="item.img" alt="">
          <!-- 中奖的奖品 -->
          <span>{{ index }}</span>
        </div>
      </div>
      <div class="go_btn" @click="move">GO</div>
    </div>
  </div>
</template>
<script>
export default {
  data () {
    return {
      list: [ // 动画执行的顺序是按照id的顺序
        { id: 1, gift: 1 },
        { id: 2, gift: 2 },
        { id: 3, gift: 3 },
        { id: 8, gift: 4 },
        { id: 4, gift: 5 },
        { id: 7, gift: 6 },
        { id: 6, gift: 7 },
        { id: 5, gift: 8 }
      ],
      animationFlag: true,
      isLoading: false, // 请求锁
      showPrize: false,
      lastIndex: 0, // 上次停留位置 index
      stopIndex: 999, // 服务器返回停留位置 index
      timer1: null, // 正常转动动画
      timer2: null, // 进入低速转动动画
      timer3: null, // 低速转动动画
      isMoving: false, // 正在执行动画
      times: 0, // 转动次数 最小12次（一圈半） 防止转动时间过短
      timerEnd: false, // 进入低速转动动画 标识
      activeIndex: 0 // 转动时高亮的标志
    }
  },
  methods: {
    randPrize () {
      // 模拟接口请求 
      // 接口请求比较快 可以如果接口请求结束的时间-调接口的时间<2秒 就settimeout 2秒后再执行代码
      setTimeout(() => {
        // 接口成功的事件处理
        this.timerEnd = true
        this.stopIndex = 4 // id 就是对应奖项的索引 一般是接口返回的奖项
        this.isLoading = false
      }, 2000)
    },
    move () { // 点击开始游戏按钮
      if (this.isMoving) { // 动画正在执行
        return
      }
      if (this.isLoading) { // 接口正在请求
        return
      }
      // if (!this.leftPrizeTime) { // 没有抽奖次数
      //   return
      // }
      // if (this.showPrize) { // 抽奖结束展示奖品
      //   return
      // }
      this.isMoving = true
      this.isLoading = true
      this.timerEnd = false
      this.times = 0
      this.animationFlag = false
      // 请求接口
      this.randPrize()
      // 执行动画
      this.timer1 = setInterval(() => {
        this.times++
        if (this.activeIndex >= 8) {
          this.activeIndex = 0
        }
        this.activeIndex += 1
        // 当获取到服务器数据 index
        if (this.timerEnd) {
          clearInterval(this.timer1)
          // 渐缓的动画
          this.enter(this.activeIndex, this.stopIndex)
        }
      }, 100)
    },
    enter (cur, stop) { // 计算需要停止的index
      let count = stop - cur
      if (count <= 4) {
        count = count > -4 ? count + 8 : count + 16
      }
      this.timer2 = setInterval(() => {
        count--
        if (this.activeIndex >= 8) {
          this.activeIndex = 0
        }
        this.activeIndex += 1
        if (count === 4) {
          clearInterval(this.timer2)
          this.stop()
        }
      }, 200)
    },
    stop () { // 计算需要停止的index
      let count = 0
      this.timer3 = setInterval(() => {
        count++
        if (this.activeIndex >= 8) {
          this.activeIndex = 0
        }
        this.activeIndex += 1
        if (count === 4) { // 抽奖动画结束
          this.lastIndex = this.stopIndex
          this.animationFlag = true
          clearInterval(this.timer3)
          setTimeout(() => {
            // this.showPrize = true // 弹窗显示中奖的商品
            this.isMoving = false
          }, 2500)
        }
      }, 400)
    },
    clearTimer () {
      clearInterval(this.timer1)
      clearInterval(this.timer2)
      clearInterval(this.timer3)
      this.timer1 = this.timer2 = this.timer3 = null
    },
  }
}
</script>
<style lang="scss" scoped>
.luck{
  width: 100%;
  height: 8rem;
  .lucky_box{
    width: 5.38rem;
    height: 4.9rem;
    position: absolute;
    left: 50%;
    top: 0.84rem;
    transform: translateX(-50%);
    display: grid;
    grid-template-columns: 1.7rem 1.7rem 1.7rem;
    grid-template-rows: 33.33% 33.33% 33.33%;
    justify-content: space-between;
    background: #FEDC7A;
    border-radius: 0.2rem;
    padding: 0.2rem;
  }
  .lucky_bg{
    width: 1.7rem;
    height: 1.5rem;
    position: relative;
    background: orange;
    border-radius: .2rem;
    overflow: hidden;
  }
  .lucky_bg:nth-of-type(5){
    grid-column-start: 3;
  }
  .lucky_item{
    width: 1.7rem;
    height: 1.5rem;
    background: #fff;
    display: flex;
    align-items: center;
    justify-content: center;
    position: relative;
    z-index: 2;
    .pig{
      width: 1.2rem;
    }
    &.play_active{
      background: orange;
      &.luck_animation{
        transform: rotateY(90deg);
        transition: transform 2s linear;
      }
    }
  }
  .prize{
    width: 100%;
    height: 100%;
    position: absolute;
    left: 0;
    top: 0;
    display: flex;
    justify-content: center;
    align-items: center;
    text-align: center;
    font-size: 0.24rem;
    color: #fff;
  }
  .go_btn{
    width: 1.7rem;
    height: 1.5rem;
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
    margin-top: -0.09rem;
    font-size: 0.48rem;
    line-height: 1.5rem;
    color: #fff;
    background: coral;
    border-radius: .2rem;
  }
}
</style>
