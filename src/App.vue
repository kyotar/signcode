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
        <textarea v-model="code" disabled></textarea>
      </section>

      <section>
        <ButtonIcon icon="⬆️" @method="addIcon" />
        <ButtonIcon icon="➡️" @method="addIcon" />
        <ButtonIcon icon="⬇️" @method="addIcon" />
        <ButtonIcon icon="⬅️" @method="addIcon" />
      </section>

      <section>
        <button @click="play">再生</button>
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
      code: '',
      output: '',
    }
  },

  mounted(){
    anime({
      targets: '.avatar',
      translateX: 250,
      rotate: '1turn',
      duration: 1000,

      begin: function(anim) {
        console.log(111, anim.began)
      },
      complete: function(anim) {
        console.log(222, anim.completed)
      },
    })
  },

  methods: {
    addIcon(icon) {
      this.code = this.code + icon
    },

    play() {
      this.command(split(this.code)[0])
    },

    command(val) {
      switch(val) {
        case '⬇️':
          console.log('下')
          break
        default:
          this.output = '🚧エラーです'
      }
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
