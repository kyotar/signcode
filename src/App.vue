<template>
  <section id="app">
    <header>
      <h1>signcode</h1>
    </header>

    <main>
      <p>メッセージ {{ output }}</p>
      <section>
        <img src="./assets/img/logo.png" class="avatar" alt="avatar">
      </section>

      <section>
        <textarea v-model="input"></textarea>
      </section>

      <section>
        <ButtonIcon icon="⬆️" @method="addIcon" />
        <ButtonIcon icon="⬇️" @method="addIcon" />
        <ButtonIcon icon="⬅️" @method="addIcon" />
        <ButtonIcon icon="➡️" @method="addIcon" />
      </section>

      <section>
        <button @click="play">うごかす</button>
        <button @click="pause">とめる</button>
        <!-- <button @click="reset">ぜんぶけす</button> -->
      </section>
    </main>

    <footer>
      <small>©signcode</small>
    </footer>
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
      output: '',
      instance: null,
      nowFrame: 0,
      maxFrame: 0,
    }
  },

  mounted() {

  },

  methods: {
    addIcon(icon) {
      this.input = this.input + icon
    },

    play() {
      this.code = split(this.input)
      this.maxFrame = this.code.length
      this.command(this.code[this.nowFrame])
    },

    pause() {
      this.instance.pause()
    },

    reset() {
      this.input = ''
      this.code = []
      this.nowFrame = 0
      this.maxFrame = 0

      anime({
        targets: '.avatar',
        translateX: 0,
        translateY: 0,
        duration: 1000,
        easing: 'easeInBack',

        complete: () => {
          this.output = ''
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
        default:
          this.output = '🚧コマンドエラーです'
      }
    },

    animate(type) {
      let poX
      let poY
      let len = 50
      this.nowFrame++
      console.log(`アニメ ${this.nowFrame} 回目`)

      switch(type) {
        case 'top':
          poY = `-=${len}px`
          break
        case 'bottom':
          poY = `+=${len}px`
          break
        case 'left':
          poX = `-=${len}px`
          break
        case 'right':
          poX = `+=${len}px`
          break
        default:
          this.output = '🚧アニメーションエラーです'
      }

      this.instance = anime({
        targets: '.avatar',
        translateX: poX,
        translateY: poY,
        duration: 200,
        easing: 'easeInOutBack',

        complete: () => {
          if(this.nowFrame < this.maxFrame) { // アニメーション終了後、次のアニメーションを開始
            this.command(this.code[this.nowFrame])
          } else if(this.nowFrame === this.maxFrame) { //アニメーション最後
            this.output = '終了でーす'
            this.reset()
          }
        }
      })
    },
  },
}
</script>

<style lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  text-align: center;

  .avatar {
    width: 100px;
    height: auto;
  }
}


</style>
