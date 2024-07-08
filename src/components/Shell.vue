<template>
  <div :class="['shell', {'disabled': disabled}]" @click="handleClick">
    <div v-if="showPearl" class="pearl"></div>
  </div>
</template>

<script>
import "../assets/style.css"
export default {
  props: {
    disabled: Boolean,
    showPearl: Boolean
  },
  methods: {
    handleClick() {
      if (!this.disabled)
        this.$emit('guess');
    }
  }
}
</script>