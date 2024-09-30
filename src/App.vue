<script setup lang="ts">
import { ref, type Ref, onMounted, watch } from 'vue'
import type { Feedback } from './types'
import { Toaster } from './components/ui/sonner'
import { toast } from 'vue-sonner'
import { ScrollArea } from '@/components/ui/scroll-area'
import { Textarea } from '@/components/ui/textarea'

import FeedbackItem from '@/components/FeedbackItem.vue'

//state
const userInput = ref('')
const feedbackList: Ref<Feedback[]> = ref([])

// actions
const addFeedbackItem = () => {
  feedbackList.value.push({ id: feedbackList.value.length + 1, content: userInput.value })
  userInput.value = ''
}

const deleteFeedbackItem = (id: number) => {
  feedbackList.value = feedbackList.value.filter((item) => item.id !== id)
}

const copyFeedbackToClipboard = (id: number) => {
  const feedback = feedbackList.value.find((item) => item.id === id)

  if (!feedback) return
  navigator.clipboard.writeText(feedback?.content)
  toast('Feedback copied!')
}

// watcher
watch(
  feedbackList,
  (newVal) => {
    localStorage.setItem('feedback', JSON.stringify(newVal))
  },
  { deep: true }
)

// lifecycle
onMounted(() => {
  const feedback = localStorage.getItem('feedback')
  feedback ? (feedbackList.value = JSON.parse(feedback)) : (feedbackList.value = [])
})
</script>

<template>
  <header class="lg:flex lg:place-items-center mb-8 lg:mb-0 leading-6">
    <div class="text-center lg:flex lg:place-items-start lg:flex-wrap space-y-4 lg:text-start">
      <h1 class="text-3xl font-bold text-emerald-400">Feedback Hub</h1>
      <p>
        Add your most used feedback responses here to copy on command. All data is save to your
        browser's local storage.
      </p>
      <Textarea
        v-model="userInput"
        @keyup.enter="addFeedbackItem"
        rows="5"
        placeholder="Add feedback here..."
        class="bg-slate-700/25 border-slate-700/50 focus:border-emerald-400 focus:ring-emerald-400 scrollbar-thin scrollbar-thumb-emerald-400 scrollbar-track-slate-700"
      />
    </div>
  </header>

  <main class="flex">
    <ScrollArea class="w-full lg:max-h-[600px] rounded-md p-4 space-y-4" type="scroll">
      <FeedbackItem
        v-for="{ id, content } of feedbackList"
        :key="id"
        :content="content"
        @copy="copyFeedbackToClipboard(id)"
        @delete="deleteFeedbackItem(id)"
      />
    </ScrollArea>
  </main>
  <Toaster position="bottom-center" theme="dark" />
</template>

<style scoped>
@media (min-width: 1024px) {
  header {
    padding-right: calc(var(--section-gap) / 2);
  }
}
</style>
