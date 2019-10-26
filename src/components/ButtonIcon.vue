<template>
  <button type="button" @click="method">{{ icon }}</button>
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

</style>
