<template>
  <button type="button" class="btn" :class="{ upsideDown: type === 'upsideDown' }" @click="method">
    {{ icon }}
  </button>
</template>

<script>
export default {
  name: 'ButtonIcon',

  props: {
    icon: {
      type: String,
      required: true,
    },
    type: {
      type: String,
      required: false,
    },
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
  line-height: 1;
  width: 64px;
  height: 64px;
  text-align: center;
  vertical-align: baseline;
  margin: 8px;
  padding: 12px;
  border: 1px solid $color-gray;
  border-radius: 8px;
  transition: all .2s ease-in;

  &:active {
    transform: scale(0.75, 0.75);
    background-color: $color-gray;
  }

  // 反転
  &.upsideDown {
    transform: scale(-1, 1);
    &:active {
      transform: scale(-0.75, 0.75);
    }
  }
}
</style>
