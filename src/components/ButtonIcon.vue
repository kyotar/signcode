<template>
  <button type="button" class="btn" @click="method">{{ icon }}</button>
</template>

<script>
export default {
  name: 'ButtonIcon',

  props: {
    icon: String
  },

  methods: {
    method() {
      this.$emit('method', this.icon)
    },
  },
}
</script>

<style lang="scss" scoped>
@import '../assets/css/common.scss';

.btn {
  font-size: 32px;
  margin: 0 16px;
}
</style>
