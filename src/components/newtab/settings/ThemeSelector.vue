<template>
  <div class="mb-12">
    <div>
      <p class="mb-4 font-semibold text-lg">{{ t('settings.themeColor') }}</p>
      <p class="mb-4 text-sm text-gray-600 dark:text-gray-400">
        {{ t('settings.themeColorDesc') }}
      </p>
    </div>

    <div class="flex flex-wrap items-center gap-6">
      <div
        v-for="theme in themeColors"
        :key="theme.id"
        class="flex flex-col items-center cursor-pointer group"
        role="button"
        :title="theme.name"
        @click="selectTheme(theme.id)"
        @keydown.enter="selectTheme(theme.id)"
        tabindex="0"
      >
        <div
          class="w-16 h-16 rounded-lg shadow-md transition-all duration-200 group-hover:shadow-lg"
          :class="[
            'relative flex items-center justify-center text-3xl',
            {
              'ring-2 ring-offset-2 ring-accent scale-110':
                selectedTheme === theme.id,
              'group-hover:scale-105': selectedTheme !== theme.id,
            },
          ]"
          :style="{ backgroundColor: theme.colorHex }"
        >
          {{ theme.emoji }}
        </div>
        <span
          class="mt-3 text-sm font-medium text-gray-700 dark:text-gray-300 text-center group-hover:text-accent transition-colors"
        >
          {{ theme.name }}
        </span>
        <span
          v-if="selectedTheme === theme.id"
          class="mt-1 text-xs text-accent font-semibold"
        >
          ✓ {{ t('common.selected') }}
        </span>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';
import { useI18n } from 'vue-i18n';

const { t } = useI18n();

const themeColors = [
  {
    id: 'classic-blue',
    name: '經典藍',
    emoji: '🟦',
    colorHex: '#3b82f6', // Classic blue
  },
  {
    id: 'comfort-green',
    name: '舒適綠',
    emoji: '🟩',
    colorHex: '#10b981', // Comfort green
  },
  {
    id: 'midnight-black',
    name: '暗夜黑',
    emoji: '⬛',
    colorHex: '#1f2937', // Midnight black
  },
  {
    id: 'taro-purple',
    name: '芋頭紫',
    emoji: '🟪',
    colorHex: '#a855f7', // Taro purple
  },
  {
    id: 'aurora-yellow',
    name: '極光黃',
    emoji: '🟨',
    colorHex: '#fbbf24', // Aurora yellow
  },
];

const selectedTheme = ref('classic-blue');

function selectTheme(themeId) {
  selectedTheme.value = themeId;
  applyThemeColor(themeId);
  // 保存到 localStorage
  localStorage.setItem('preferredThemeColor', themeId);
  // 派发事件，通知其他組件
  window.dispatchEvent(
    new CustomEvent('theme-color-changed', {
      detail: { themeColor: themeId },
    })
  );
}

function applyThemeColor(themeId) {
  const theme = themeColors.find((t) => t.id === themeId);
  if (!theme) return;

  // 應用 CSS 變數到根元素
  const root = document.documentElement;
  root.style.setProperty('--theme-color', theme.colorHex);
  root.style.setProperty('--theme-color-light', theme.colorHex + '20');
  root.style.setProperty('--theme-color-dark', theme.colorHex + 'dd');
}

// 初始化
function initThemeColor() {
  const saved = localStorage.getItem('preferredThemeColor');
  if (saved && themeColors.some((t) => t.id === saved)) {
    selectedTheme.value = saved;
    applyThemeColor(saved);
  } else {
    // 預設為經典藍
    applyThemeColor('classic-blue');
  }
}

// 組件掛載時初始化
initThemeColor();
</script>

<style scoped>
/* 主題色樣式 */
:root {
  --theme-color: #3b82f6;
  --theme-color-light: #3b82f620;
  --theme-color-dark: #3b82f6dd;
}

.accent {
  color: var(--theme-color);
}
</style>
