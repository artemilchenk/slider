<template>
  <div @click="$emit('click')">
    <slot />
  </div>
</template>

<script setup>
defineEmits(["click"]);
</script>

<style scoped lang="scss"></style>
