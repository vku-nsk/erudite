<template>
  <button
    class="btn-command"
    @click.stop='$emit("command")'
  >
  <slot></slot>
  </button>
</template>

<script lang="ts">
import { defineComponent, PropType } from "vue";

export default defineComponent({  
  name: "CommandButton",
  emits: ["command"]
})
</script>

<style scoped>

.btn-command {
  display: block;
  text-align:center;
  font-size: inherit;
  border-radius: 6px;
}
</style>