<template>
  <img
    :src="img"
    :class="['image', { selected: isSelected }]"
    @click="$emit('click')"
  >
</template>

<script setup lang="ts">
interface Props {
  img: string;
  isSelected?: boolean;
}

const { img, isSelected } = defineProps<Props>();

defineEmits(["click"]);
</script>

<style scoped lang="scss">
.image {
  display: block;
  width: 100%;
  height: 140px;
  object-fit: cover;
  cursor: pointer;
  border-radius: 10px;
  transition:
    transform 0.3s ease,
    box-shadow 0.5s ease;

  &:hover {
    transform: scale(1.05);
  }

  &.selected {
    box-shadow: 0 0 0 4px #3b82f6;
    transform: scale(0.9);
  }
}
</style>
