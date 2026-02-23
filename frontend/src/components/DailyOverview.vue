<script setup lang="ts">
import { computed } from 'vue'

const props = defineProps<{
  dailyGifts: any
}>()

const growth = computed(() => props.dailyGifts?.growth)
const gifts = computed(() => props.dailyGifts?.gifts || [])

function formatTime(timestamp: number) {
  if (!timestamp) return '未领取'
  const d = new Date(timestamp)
  return `${d.getHours().toString().padStart(2, '0')}:${d.getMinutes().toString().padStart(2, '0')}`
}

function getStatusClass(done: boolean, enabled: boolean) {
  if (done) return 'text-green-600 bg-green-50 dark:text-green-400 dark:bg-green-900/20'
  if (enabled) return 'text-blue-600 bg-blue-50 dark:text-blue-400 dark:bg-blue-900/20'
  return 'text-gray-500 bg-gray-50 dark:text-gray-400 dark:bg-gray-800'
}

function getStatusText(done: boolean, enabled: boolean) {
  if (done) return '已完成'
  if (enabled) return '进行中'
  return '未开启'
}
</script>

<template>
  <div class="flex flex-col gap-4">
    <!-- Growth Task -->
    <div v-if="growth" class="rounded-lg bg-white p-4 shadow dark:bg-gray-800">
      <div class="mb-3 flex items-center justify-between">
        <h3 class="flex items-center gap-2 font-medium">
          <div class="i-carbon-growth text-green-500" />
          <span>{{ growth.label || '成长任务' }}</span>
        </h3>
        <span 
          class="rounded px-2 py-0.5 text-xs font-bold"
          :class="getStatusClass(growth.doneToday, true)"
        >
          {{ growth.doneToday ? '今日已完成' : `${growth.completedCount}/${growth.totalCount}` }}
        </span>
      </div>
      
      <div v-if="growth.tasks && growth.tasks.length" class="space-y-2">
        <div 
          v-for="(task, idx) in growth.tasks" 
          :key="idx"
          class="flex items-center justify-between text-sm"
        >
          <span class="text-gray-600 dark:text-gray-400">{{ task.desc || task.name }}</span>
          <span class="text-xs text-gray-500">{{ task.current }}/{{ task.target }}</span>
        </div>
      </div>
      <div v-else class="text-center text-sm text-gray-400">
        暂无任务详情
      </div>
    </div>

    <!-- Daily Gifts Grid -->
    <div class="rounded-lg bg-white p-4 shadow dark:bg-gray-800">
      <h3 class="mb-3 flex items-center gap-2 font-medium">
        <div class="i-carbon-gift text-pink-500" />
        <span>每日礼包 & 任务</span>
      </h3>
      
      <div class="grid grid-cols-2 gap-3 sm:grid-cols-3">
        <div 
          v-for="gift in gifts" 
          :key="gift.key"
          class="flex flex-col justify-between rounded border border-gray-100 p-2 dark:border-gray-700"
        >
          <div class="mb-1 text-sm font-medium text-gray-700 dark:text-gray-300">
            {{ gift.label }}
          </div>
          
          <div class="flex items-end justify-between">
            <div class="flex flex-col">
              <span 
                class="text-xs"
                :class="gift.doneToday ? 'text-green-500' : (gift.enabled ? 'text-blue-500' : 'text-gray-400')"
              >
                {{ getStatusText(gift.doneToday, gift.enabled) }}
              </span>
              <span v-if="gift.doneToday" class="text-[10px] text-gray-400">
                {{ formatTime(gift.lastAt) }}
              </span>
            </div>
            
            <div v-if="gift.totalCount > 0" class="text-xs font-bold text-gray-500">
              {{ gift.completedCount }}/{{ gift.totalCount }}
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
