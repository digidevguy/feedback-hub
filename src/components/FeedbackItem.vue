<script setup lang="ts">
const { content } = defineProps<{
  content: string
}>()
import IconBin from '@/components/icons/IconBin.vue'
import IconCopy from '@/components/icons/IconCopy.vue'
import { Button } from '@/components/ui/button'
</script>

<template>
  <div
    class="flex justify-between gap-4 items-center w-full border border-slate-700/50 py-2 px-4 rounded-md mb-4 last:mb-0 transition-all duration-75 ease-in-out"
  >
    <p>{{ content }}</p>
    <div class="flex flex-col space-y-4">
      <Button
        variant="ghost"
        class="border border-slate-700/50 bg-slate-800/25 p-2 w-10 h-10 hover:border-emerald-400 hover:bg-emerald-400/25 hover:text-emerald-400"
        @click="$emit('copy')"
      >
        <IconCopy />
      </Button>
      <Button
        variant="ghost"
        class="border border-slate-700/50 bg-slate-800/25 p-2 w-10 h-10 hover:border-emerald-400 hover:bg-emerald-400/25 hover:text-emerald-400"
        @click="$emit('delete')"
      >
        <IconBin />
      </Button>
    </div>
  </div>
</template>
