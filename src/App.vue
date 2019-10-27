<template>
  <section id="app">
    <header>
      <h1>signcode</h1>
    </header>

    <main class="main">
      <p>🐰{{ output }}</p>
      <section class="stage">
        <img src="./assets/img/rabbit.png" class="avatar" alt="avatar">
      </section>

      <section>
        <input type="text" placeholder="コマンドを入力してね" class="commandArea" v-model="input" disabled />
      </section>

      <section>
        <ButtonIcon icon="⬆️" @method="addIcon" />
        <ButtonIcon icon="⬇️" @method="addIcon" />
        <ButtonIcon icon="⬅️" @method="addIcon" />
        <ButtonIcon icon="➡️" @method="addIcon" />
        <ButtonIcon icon="🤾" @method="addIcon" />
        <ButtonIcon icon="🔄" @method="addIcon" />
      </section>
    </main>

    <section class="naviBar">
      <button class="naviBtn" :class="{ disabled: isDisabled }" @click="action" :disabled="isDisabled">{{ btnLabel }}</button>
      <!-- <button >ひとつけす</button> -->
      <!-- <button @click="pause">とめる</button> -->
      <!-- <button @click="reset">ぜんぶけす</button> -->
    </section>
  </section>
</template>

<script>
import split from 'graphemesplit'
import anime from 'animejs/lib/anime.es.js'
import ButtonIcon from './components/ButtonIcon.vue'

export default {
  name: 'app',

  components: {
    ButtonIcon,
  },

  data() {
    return {
      input: '',
      code: [],
      output: 'やぁ、こんにちは！',
      instance: null,
      nowFrame: 0,
      maxFrame: 0,
      parse: 2,
      isPlaying: false,
      isBreak: false,
    }
  },

  computed: {
    // ナビボタンの名称
    btnLabel() {
      return this.isPlaying ? 'とめる' : 'うごかす'
    },

    // 非アクティブ
    isDisabled() {
      return this.isBreak || this.emptyInput || this.isMaxFrame
    },

    // コマンドが空の場合
    emptyInput() {
      return !(split(this.input).length)
    },

    // 最終フレームの場合
    isMaxFrame() {
      return (this.nowFrame === this.maxFrame) && (this.maxFrame > 0)
    },
  },

  mounted() {
  },

  methods: {
    addIcon(icon) {
      this.input = this.input + icon
    },

    action() {
      if(this.isPlaying) {
        this.pause()
      } else {
        this.play()
      }
    },

    play() {
      this.code = split(this.input)
      this.maxFrame = this.code.length
      this.isPlaying = true
      this.output = 'レッツアニメーションスタート！'
      this.command(this.code[this.nowFrame])
    },

    pause() {
      this.instance.pause()
      this.isPlaying = false
    },

    reset() {
      this.input = ''
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
      switch(val) {
        case '⬆️':
          this.animate('top')
          break
        case '⬇️':
          this.animate('bottom')
          break
        case '⬅️':
          this.animate('left')
          break
        case '➡️':
          this.animate('right')
          break
        case '🤾':
          this.animate('jump')
          break
        case '🔄':
          this.animate('spin')
          break
        default:
          this.output = `🚧コマンドエラーです。- ${val} -`
      }
    },

    animate(type) {
      // デフォルト値
      let poX
      let poY
      let roY
      let scl
      let len = 40
      let time = 200
      let ease = 'easeInOutBack'
      let dire = 'normal'
      this.nowFrame++
      console.info(`アニメ ${this.nowFrame} 回目`)

      switch(type) {
        case 'top':
          poY = `-=${ len }px`
          scl = `*=0.9`
          this.parse = this.parse * 0.8
          break
        case 'bottom':
          poY = `+=${ len }px`
          scl = `*=1.1`
          this.parse = this.parse * 1.2
          break
        case 'left':
          poX = `-=${ len }px`
          break
        case 'right':
          poX = `+=${ len }px`
          break
        case 'jump':
          // poY = [`-=${ len * this.parse }px`]
          poY = ['+=0px', '+=4px', `-=${ len * this.parse }px`]
          time = 800
          ease = 'easeOutQuart'
          dire = 'alternate'
          break
        case 'spin':
          roY = ['0deg', '360deg']
          time = 400
          ease = 'easeInOutQuint'
          break
        default:
          this.output = '🚧アニメーションエラーです'
      }

      this.instance = anime({
        targets: '.avatar',
        translateX: poX,
        translateY: poY,
        rotateY: roY,
        scale: scl,
        duration: time,
        easing: ease,
        direction: dire,

        complete: () => {
          if(this.nowFrame < this.maxFrame) { // アニメーション終了後、次のアニメーションを開始
            this.command(this.code[this.nowFrame])
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
  text-align: center;
  background-color: $color-grayWhite;
  height: 100%;

  .main {
    width: 320px;
    margin: 0 auto;

    .stage {
      width: 100%;
      height: 320px;
      background-color: $color-white;
      border-radius: 32px;
      margin: 0 auto;
      overflow: hidden;
      display: flex;
      align-items: center;
      justify-content: center;

      .avatar {
        width: 100px;
        height: auto;
      }
    }

    .commandArea {
      width: 100%;
      padding: 8px;
      margin: 16px 0;
      border-radius: 16px;
      font-size: $text-small;
      background-color: $color-primaryDark;

      &::placeholder {
        color: $color-white;
      }
    }
  }

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
      box-shadow: 0 -10px 10px rgba(0, 0, 0, .1);

      &.disabled {
        color: rgba($color-white, 0.3);
      }
    }
  }
}


</style>
