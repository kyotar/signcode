<template>
  <section id="app">
    <header>
      <h1>signcode</h1>
    </header>

    <main>
      <p>メッセージ {{ output }}</p>
      <section>
        <transition name="fade">
          <img src="./assets/img/logo.png" class="avatar" alt="avatar">
        </transition>
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
import ButtonIcon from './components/ButtonIcon.vue'
import split from 'graphemesplit'

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

  .fade-enter-active, .fade-leave-active {
    transition: opacity .5s;
  }
  .fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
    opacity: 0;
  }
}


</style>
