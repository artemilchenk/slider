<template>
  <div class="carousel">
    <CarouselNavButton class="nav-button-wrapper prev" @click="prev">
      <slot name="prev-button"> prev </slot>
    </CarouselNavButton>

    <div ref="carouselRef" class="viewport">
      <div
        ref="trackRef"
        class="track"
        :style="trackStyle"
        @transitionend="onTransitionEnd"
      >
        <div
          v-for="img in loopedImages"
          :key="img"
          class="item"
          :style="{ width: `${itemWidth}%` }"
        >
          <div @click="onItemClick(img)">
            <slot name="item" :img="img" :selected="selected.includes(img)">
              <CarouselItem :img="img" :is-selected="selected.includes(img)" />
            </slot>
          </div>
        </div>
      </div>
    </div>

    <CarouselNavButton class="nav-button-wrapper next" @click="next">
      <slot name="next-button"> next </slot>
    </CarouselNavButton>
  </div>
</template>

<script setup>
import { computed, ref, watch } from "vue";
import CarouselNavButton from "../ui/CarouselNavButton.vue";
import CarouselItem from "./CarouselItem.vue";

const emit = defineEmits(["selectChange"]);

const props = defineProps({
  images: {
    type: Array,
    default: () => [],
  },
  itemsOnView: { type: Number, default: 4 },
});

const index = ref(0);
const selected = ref([]);
const itemWidth = computed(() => 100 / props.itemsOnView);
const loopedImages = ref([]);
const isAnimating = ref(false);
const transitionEnabled = ref(true);
const action = ref("");
const trackRef = ref(null);

watch(
  () => props.images,
  (images) => {
    loopedImages.value = [...images];
  },
  { immediate: true },
);

const trackStyle = computed(() => {
  return {
    transform: `translateX(${-itemWidth.value * index.value}%)`,
    transition: transitionEnabled.value ? "transform 0.4s ease" : "none",
  };
});

async function prev() {
  if (isAnimating.value || loopedImages.value.length <= 1) return;
  isAnimating.value = true;
  action.value = "prev";

  const last = loopedImages.value.pop();
  if (last !== undefined) loopedImages.value.unshift(last);

  transitionEnabled.value = false;
  index.value = 1;

  requestAnimationFrame(() => {
    transitionEnabled.value = true;
    index.value = 0;
  });
}

async function next() {
  if (isAnimating.value || loopedImages.value.length <= 1) return;
  isAnimating.value = true;
  transitionEnabled.value = true;
  action.value = "next";
  index.value = 1;
}

async function onTransitionEnd(event) {
  if (
    event.target !== event.currentTarget ||
    event.propertyName !== "transform"
  ) {
    return;
  }

  if (!action.value || !loopedImages.value.length) return;

  if (action.value === "next") {
    const first = loopedImages.value.shift();
    if (first !== undefined) loopedImages.value.push(first);
    transitionEnabled.value = false;
    index.value = 0;
  }

  action.value = null;
  isAnimating.value = false;
}

function toggleSelectItem(img) {
  const isSelected = selected.value.includes(img);

  let nextSelected;

  if (isSelected) {
    nextSelected = selected.value.filter((i) => i !== img);
  } else {
    nextSelected = [...selected.value, img];
  }

  selected.value = nextSelected;

  emit("selectChange", {
    item: img,
    selected: nextSelected,
    isSelected: !isSelected,
  });
}

function onItemClick(img) {
  toggleSelectItem(img);
}
</script>

<style scoped lang="scss">
.carousel {
  position: relative;

  .viewport {
    overflow: hidden;
  }

  .track {
    display: flex;
  }

  .item {
    box-sizing: border-box;
    flex-shrink: 0;
    padding: 4px;
  }

  .nav-button-wrapper {
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
    z-index: 1;

    transition:
      opacity 0.2s ease,
      scale 0.2s ease;

    &:hover {
      opacity: 0.8;
      scale: 1.2;
    }

    &.prev {
      left: 8px;
    }

    &.next {
      right: 8px;
    }
  }
}

@media (max-width: 768px) {
  .carousel {
    .item {
      width: 100% !important;
    }
  }
}
</style>
