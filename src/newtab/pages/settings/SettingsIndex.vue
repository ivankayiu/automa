<template>
  <div>
    <!-- 主題顏色選擇器 -->
    <theme-selector />

    <!-- 主題亮度選擇器 -->
    <div class="mb-12">
      <p class="mb-1 font-semibold">{{ t('settings.theme') }}</p>
      <div class="flex items-center space-x-4">
        <div
          v-for="item in theme.themes"
          :key="item.id"
          class="cursor-pointer"
          role="button"
          @click="theme.set(item.id)"
        >
          <div
            :class="{ 'ring ring-accent': item.id === theme.activeTheme.value }"
            class="rounded-lg p-0.5"
          >
            <img
              :src="require(`@/assets/images/theme-${item.id}.png`)"
              width="140"
              class="rounded-lg"
            />
          </div>
          <span class="ml-1 text-sm text-gray-600 dark:text-gray-200">
            {{ item.name }}
          </span>
        </div>
      </div>
    </div>

    <!-- 語言選擇 -->
    <div class="flex items-center">
      <div id="languages">
        <p class="mb-1 font-semibold">{{ t('settings.language.label') }}</p>
        <ui-select
          :model-value="settings.locale"
          class="w-80"
          @change="updateLanguage"
        >
          <option
            v-for="locale in supportLocales"
            :key="locale.id"
            :value="locale.id"
          >
            {{ locale.name }}
          </option>
        </ui-select>
        <a
          class="ml-1 block text-gray-600 dark:text-gray-200"
          href="https://github.com/AutomaApp/automa/wiki/Help-Translate"
          target="_blank"
          rel="noopener"
        >
          {{ t('settings.language.helpTranslate') }}
        </a>
      </div>
      <p class="ml-4 inline-block" v-if="isLangChange">
        {{ t('settings.language.reloadPage') }}
      </p>
    </div>

    <!-- 日誌設置 -->
    <div class="mt-12" id="delete-logs">
      <p class="mb-1 font-semibold">Workflow Logs</p>
      <div class="flex items-center">
        <ui-select
          :model-value="settings.deleteLogAfter"
          :label="t('settings.deleteLog.title')"
          placeholder="Delete after"
          class="w-80"
          @change="
            updateSetting(
              'deleteLogAfter',
              $event === 'never' ? 'never' : +$event
            )
          "
        >
          <option :key="day" :value="day" v-for="day in deleteLogDays">
            <template v-if="typeof day === 'string'">
              {{ t('settings.deleteLog.deleteAfter.never') }}
            </template>
            <template v-else="">
              {{ t('settings.deleteLog.deleteAfter.days', { day }) }}
            </template>
          </option>
        </ui-select>
        <ui-input
          :model-value="settings.logsLimit"
          class="ml-4"
          type="number"
          label="Logs limit"
          min="10"
          @change="updateSetting('logsLimit', +$event <= 0 ? 1000 : +$event)"
        />
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed, ref } from 'vue';
import { useI18n } from 'vue-i18n';
import { useStore } from '@/stores/main';
import { useTheme } from '@/composable/theme';
import { supportLocales } from '@/utils/shared';
import ThemeSelector from '@/components/newtab/settings/ThemeSelector.vue';

const deleteLogDays = ['never', 7, 14, 30, 60, 120];
const { t } = useI18n();
const store = useStore();
const theme = useTheme();
const isLangChange = ref(false);
const settings = computed(() => store.settings);

function updateSetting(path, value) {
  store.updateSettings({ [path]: value });
}

function updateLanguage(value) {
  isLangChange.value = true;
  updateSetting('locale', value);
}
</script>
