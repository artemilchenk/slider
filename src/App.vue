<template>
  <div class="app">
    <h1>Image Carousel</h1>

    <div
      ref="carouselRef"
      class="carousel"
    >
      <Carousel
        :images="images"
        :items-on-view="itemsOnView"
        @select-change="handleSelect"
      >
        <template #item="{ img, selected: isSelected }">
          <CarouselItem
            :img="img"
            :is-selected="isSelected"
          />
        </template>
        <template #prev-button>
          <div class="button">
            <div class="button-content">
              ◀
            </div>
          </div>
        </template>
        <template #next-button>
          <div class="button">
            <div class="button-content">
              ▶
            </div>
          </div>
        </template>
      </Carousel>
    </div>

    <div class="selected">
      <h2>Selected Images</h2>
      <div class="list">
        <img
          v-for="url in selected"
          :key="url"
          :src="url"
          class="item"
        >
      </div>
    </div>
  </div>
</template>

<script setup>
import { Carousel } from "./features/index.ts";
import CarouselItem from "./features/carousel/components/CarouselItem.vue";
import { onBeforeUnmount, onMounted, ref } from "vue";

const carouselRef = ref();
const images = ref([]);
const selected = ref([]);
const itemsOnView = ref(0);

function handleSelect({ selected: selectedItems }) {
  selected.value = selectedItems;
}

function generateImages(count) {
  return Array.from(
    { length: count },
    (_, i) => `https://picsum.photos/seed/${i}/600/400`,
  );
}

function updateBreakpoint() {
  const isMobile = window.matchMedia("(max-width: 768px)").matches;
  const isDesktop = window.matchMedia("(min-width: 768px)").matches;
  if (isMobile) itemsOnView.value = 1;
  if (isDesktop) itemsOnView.value = 4;
}

onMounted(() => {
  images.value = generateImages(20);
  updateBreakpoint();
  window.addEventListener("resize", updateBreakpoint);
});

onBeforeUnmount(() => {
  window.removeEventListener("resize", updateBreakpoint);
});
</script>

<style scoped lang="scss">
.app {
  width: 100%;
  max-width: 1054px;
  margin: 0 auto;

  .button {
    display: flex;
    justify-content: center;
    align-items: center;
    position: relative;
    width: 30px;
    height: 30px;
    border-radius: 50%;
    background: #1f2028;
    padding: 8px;
    cursor: pointer;
    color: darkred;

    .button-content {
    }
  }
}

.selected {
  .list {
    display: flex;
    gap: 8px;
    flex-wrap: wrap;
  }

  .item {
    width: 100px;
    height: 70px;
    object-fit: cover;
    border-radius: 8px;
    transition: transform 0.3s ease;

    &:hover {
      transform: scale(1.05);
    }
  }
}
</style>
