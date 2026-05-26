<template>
  <img
    :src="props.item"
    :class="['image', { active: isActive }]"
    @click="$emit('click')"
  />
</template>

<script setup lang="ts">
interface Props {
  item: string;
  isActive?: boolean;
}

const props = defineProps<Props>();

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

  &.active {
    box-shadow: 0 0 0 4px #3b82f6;
    transform: scale(0.9);
  }
}
</style>
