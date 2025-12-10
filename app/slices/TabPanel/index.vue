<script setup lang="ts">
import type { Content } from "@prismicio/client";
import { ref } from "vue";
import { PrismicRichText } from "@prismicio/vue";

const props = defineProps(
  getSliceComponentProps<Content.TabPanelSlice>([
    "slice",
    "index",
    "slices",
    "context",
  ])
);

const activeTab = ref(0);
const openTabs = ref<number[]>(
  props.slice.primary.tabs.map((_, index) => index)
);

const closeTab = (index: number) => {
  const tabIndex = openTabs.value.indexOf(index);
  if (tabIndex > -1) {
    openTabs.value.splice(tabIndex, 1);
    if (activeTab.value === index && openTabs.value.length > 0) {
      activeTab.value = openTabs.value[0] || 0;
    }
  }
};

const isTabOpen = (index: number) => openTabs.value.includes(index);
</script>

<template>
  <Bounded
    :data-slice-type="slice.slice_type"
    :data-slice-variation="slice.variation"
    class="tab-panel"
  >
    <div class="w-full max-w-4xl mx-auto">
      <!-- Tab Headers -->
      <div class="flex flex-wrap border-b border-gray-300 mb-6">
        <template v-for="(tab, index) in slice.primary.tabs" :key="index">
          <button
            v-if="isTabOpen(index)"
            :class="[
              'px-6 py-3 font-medium transition-all relative flex items-center gap-2',
              activeTab === index
                ? 'text-blue-600 border-b-2 border-blue-600'
                : 'text-gray-600 hover:text-gray-900',
            ]"
            @click="activeTab = index"
          >
            <span>{{ tab.label }}</span>
            <button
              v-if="'closable' in tab && tab.closable"
              class="ml-2 text-gray-400 hover:text-red-600 transition-colors"
              aria-label="Close tab"
              @click.stop="closeTab(index)"
            >
              <svg
                class="w-4 h-4"
                fill="none"
                stroke="currentColor"
                viewBox="0 0 24 24"
              >
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
                  d="M6 18L18 6M6 6l12 12"
                />
              </svg>
            </button>
          </button>
        </template>
      </div>

      <div class="tab-content">
        <template
          v-for="(tab, index) in slice.primary.tabs"
          :key="`content-${index}`"
        >
          <div
            v-if="activeTab === index && isTabOpen(index)"
            class="prose prose-lg max-w-none animate-fade-in"
          >
            <PrismicRichText :field="tab.content" />
          </div>
        </template>
      </div>

      <div v-if="openTabs.length === 0" class="text-center py-12 text-gray-500">
        <p class="text-lg">No hay pestañas disponibles</p>
      </div>
    </div>
  </Bounded>
</template>

<style scoped>
.animate-fade-in {
  animation: fadeIn 0.3s ease-in;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.tab-panel :deep(.prose) {
  @apply text-gray-700;
}

.tab-panel :deep(.prose p) {
  @apply mb-4;
}

.tab-panel :deep(.prose strong) {
  @apply font-semibold text-gray-900;
}

.tab-panel :deep(.prose a) {
  @apply text-blue-600 hover:text-blue-800 underline;
}

.tab-panel :deep(.prose ul),
.tab-panel :deep(.prose ol) {
  @apply ml-6 mb-4;
}

.tab-panel :deep(.prose li) {
  @apply mb-2;
}
</style>
