<template>
  <section id="app">
    <section class="stage">
      <img src="./assets/img/rabbit.png" class="avatar" alt="avatar">

      <output class="commandArea" :class="{ commnadPlaying: isPlaying }">
        <template v-if="input.length > 0">
          <transition-group name="list" tag="p">
            <span v-for="(icon, index) in input"
              :key="index"
              class="icon"
              :class="{ nowIcon: (index === nowFrame - 1 && isPlaying), upsideDown: icon === '🤸‍♀️' }"
              >
              {{ icon }}
            </span>
           </transition-group>
          </template>
        <template v-else>
          コマンドを入力してね
        </template>
        <span class="numberInfo">
          {{ input.length }} / {{limtiFrame}}
        </span>
        <span class="delBtn" @click="delIcon" :class="{ disabled: (input.length === 0) || isPlaying }">🔨</span>
      </output>
    </section>


  <section class="controller">
    <!-- <p>🐰{{ output }}</p> -->

    <section>
      <ButtonIcon icon="⬆️" @method="addIcon" :disabled="isLimitOver" />
      <ButtonIcon icon="⬇️" @method="addIcon" :disabled="isLimitOver" />
      <ButtonIcon icon="⬅️" @method="addIcon" :disabled="isLimitOver" />
      <ButtonIcon icon="➡️" @method="addIcon" :disabled="isLimitOver" />
      <ButtonIcon icon="🤾" @method="addIcon" :disabled="isLimitOver" />
      <ButtonIcon icon="🔄" @method="addIcon" :disabled="isLimitOver" />
      <ButtonIcon icon="🤸‍♂️" @method="addIcon" :disabled="isLimitOver" />
      <ButtonIcon icon="🤸‍♀️" @method="addIcon" :disabled="isLimitOver" type="upsideDown" />
      <ButtonIcon icon="👣" @method="addIcon" :disabled="isLimitOver" />
    </section>

  </section>

    <section class="naviBar">
      <button class="naviBtn" :class="{ disabled: isDisabled }" @click="action" :disabled="isDisabled">{{ btnLabel }}</button>
      <!-- <button >ひとつけす</button> -->
      <!-- <button @click="pause">とめる</button> -->
      <!-- <button @click="reset">ぜんぶけす</button> -->
    </section>
  </section>
</template>

<script>
// import split from 'graphemesplit'
import anime from 'animejs/lib/anime.es.js'
import ButtonIcon from './components/ButtonIcon.vue'

export default {
  name: 'app',

  components: {
    ButtonIcon,
  },

  data() {
    return {
      input: [],
      code: [],
      output: 'やぁ、こんにちは！',
      instance: null,
      nowFrame: 0,
      maxFrame: 0,
      limtiFrame: 24,
      parse: 2,
      isPlaying: false,
      isBreak: false,
    }
  },

  computed: {
    // ナビボタンの名称
    btnLabel() {
      return this.isPlaying ? 'Stop' : 'Start'
    },

    placeholder() {
      return this.input.length === 0 ? '' : 'コマンドを入力してね'
    },

    // 非アクティブ
    isDisabled() {
      return this.isBreak || this.emptyInput || this.isMaxFrame
    },

    // コマンド数オーバー
    isLimitOver() {
      return this.input.length >= this.limtiFrame
    },

    // コマンドが空の場合
    emptyInput() {
      return !(this.input.length)
    },

    // 最終フレームの場合
    isMaxFrame() {
      return (this.nowFrame === this.maxFrame) && (this.maxFrame > 0)
    },
  },

  mounted() {
    // iOSでタップカラーを変更させるための設定
    document.addEventListener('touchstart', () => {}, { passive: true })
  },

  methods: {
    // 絵文字追加
    addIcon(icon) {
      this.input.push(icon)
    },

    // 絵文字削除
    delIcon() {
      if(!this.isPlaying && this.input.length > 0) {
        this.input.pop()
      }
    },

    // 再生ボタン
    action() {
      if(this.isPlaying) {
        this.pause()
      } else {
        this.play()
      }
    },

    // 再生ボタンをタップしたとき
    play() {
      this.maxFrame = this.input.length
      this.isPlaying = true
      this.output = 'レッツアニメーションスタート！'
      this.command(this.input[this.nowFrame])
    },

    // 一時停止
    pause() {
      this.instance.pause()
      this.isPlaying = false
    },

    // リセット
    reset() {
      this.input = []
      this.code = []
      this.nowFrame = 0
      this.maxFrame = 0
      this.isBreak = true

      anime({
        targets: '.avatar',
        translateX: 0,
        translateY: 0,
        scale: 1,
        duration: 1000,
        easing: 'easeInBack',

        // 即時にボタン操作を不能
        begin: () => {
          this.isBreak = true
        },

        complete: () => {
          this.output = 'もう一回、あそぶかい？'
          this.isPlaying = false
          this.isBreak = false
          this.parse = 2
        }
      })
    },

    command(val) {
      if(val){
        this.animate(val)
      } else {
        this.output = `🚧コマンドエラーです。- ${val} -`
      }
    },

    animate(val) {
      // デフォルト値
      let poX
      let poY
      let ro
      let roX
      let roY
      let scl
      let len = 40
      let time = 200
      let ease = 'easeInOutBack'
      let dire = 'normal'
      this.nowFrame++
      console.info(`アニメ ${this.nowFrame} 回目`)

      switch(val) {
        case '⬆️':
          poY = `-=10px`
          roX = ['0deg', '30deg', '0deg']
          scl = `*=0.9`
          this.parse = this.parse * 0.8
          break
        case '⬇️':
          poY = `+=10px`
          roX = ['0deg', '-30deg', '0deg']
          scl = `*=1.1`
          this.parse = this.parse * 1.2
          break
        case '⬅️':
          ro = ['0deg', '-10deg', '0deg']
          poX = `-=${ len }px`
          break
        case '➡️':
          ro = ['0deg', '10deg', '0deg']
          poX = `+=${ len }px`
          break
        case '🤾':
          poY = [`-=${ len * this.parse }px`]
          time = 300
          ease = 'cubicBezier(0.540, -0.320, 0.540, 1.320)'
          dire = 'alternate'
          break
        case '🔄':
          roY = ['0deg', '360deg']
          time = 400
          ease = 'easeInOutQuint'
          break
        case '🤸‍♂️':
          ro = ['0deg',  '-360deg']
          poY = ['+=0px', '-=24px', '+=24px']
          poX = `-=${ len }px`
          time = 400
          ease = 'easeInOutQuint'
          break
        case '🤸‍♀️':
          ro = ['0deg',  '360deg']
          poY = ['+=0px', '-=24px', '+=24px']
          poX = `+=${ len }px`
          time = 400
          ease = 'easeInOutQuint'
          break
        case '👣':
          poY = ['+=0px', '+=4px', '-=4px']
          time = 200
          dire = 'alternate'
          break
        default:
          this.output = '🚧アニメーションエラーです'
      }

      this.instance = anime({
        targets: '.avatar',
        translateX: poX,
        translateY: poY,
        rotate: ro,
        rotateY: roY,
        rotateX: roX,
        scale: scl,
        duration: time,
        easing: ease,
        direction: dire,

        complete: () => {
          if(this.nowFrame < this.maxFrame) { // アニメーション終了後、次のアニメーションを開始
            this.command(this.input[this.nowFrame])
          } else if(this.nowFrame === this.maxFrame) { //アニメーション最後
            this.output = 'アニメーション終了でーす'
            this.reset()
          }
        }
      })
    },
  },
}
</script>

<style lang="scss">
@import './assets/css/common.scss';

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  color: $color-black;
  text-align: center;
  background-color: $color-grayWhite;
  min-height: 100%;

  // ステージ
  .stage {
    min-width: 320px;
    min-height: 240px;
    background-color: rgba($color-primary, .1);
    margin: 0 auto;
    overflow: hidden;
    display: flex;
    align-items: center;
    justify-content: center;
    position: relative;

    .avatar {
      width: 80px;
      height: auto;
    }

    // コマンド
    .commandArea {
      width: 264px;
      min-height: 48px;
      padding: 8px 56px 8px 8px;
      margin: 16px 0;
      border-radius: 16px;
      background-color: rgba($color-gray, .5);
      display: block;
      text-align: left;
      position: relative;
      font-size: $text-small;
      line-height: 1.5;
      bottom: 16px;
      position: absolute;
      transition: all .5s ease;

      &::placeholder {
        color: $color-white;
      }

      .icon {
        display: inline-block;
        padding: 4px;
        border-radius: 4px;
        transition: all .5s ease;

        &.nowIcon {
          background-color: rgba($color-primary, .3);
        }

        &.upsideDown {
          transform: scale(-1, 1);
        }
      }

      .numberInfo {
        position: absolute;
        top: 8px;
        right: 16px;
        color: $color-grayDark;
      }

      .delBtn {
        font-size: $text-medium;
        position: absolute;
        top: 12px;
        right: -32px;
        transition: all .3s ease;

        &:active {
          transform: scale(0.5, 0.5);
        }

        &.disabled {
          opacity: .5;
          pointer-events: none;
        }
      }

      &.commnadPlaying {
        opacity: .3;
      }
    }

    .list {
      &-enter-active, &-leave-active {
        transition: all 1s cubic-bezier(0.450, -0.435, 0.635, 1.405);
      }
      &-enter, &-leave-to {
        transform: translateX(16px) scale(0.1, 0.1);
      }
    }
  }

  // コントローラー
  .controller {
    // background-color: $color-grayWhite;
    // border-radius: 32px;
    margin: 16px 8px;
    // padding: 16px;
    // box-shadow: 0 0 4px 0 rgba(0, 0, 0, 0.2), 0 0 16px 0 rgba(0, 0, 0, 0.2);
    // position: fixed;
    // bottom: 64px;
  }

  // ナビゲーションバー
  .naviBar {
    width: 100%;
    bottom: 0;
    position: fixed;

    & > .naviBtn {
      width: 100%;
      padding: 16px 8px;
      margin: 0;
      font-size: $text-large;
      font-weight: bold;
      color: $color-white;
      background-color: $color-primary;
      box-shadow: 0 0 4px 0 rgba(0, 0, 0, 0.2), 0 0 16px 0 rgba(0, 0, 0, 0.2);
      transition: 0.5s all ease;

      &.disabled {
        color: rgba($color-white, 0.3);
      }
    }
  }
}


</style>
