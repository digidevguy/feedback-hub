<script setup lang="ts">
import { ref, type Ref, onMounted, watch } from 'vue'
import { toast } from 'vue-sonner'

import FeedbackItem from '@/components/FeedbackItem.vue'
import { Toaster } from './components/ui/sonner'
import { ScrollArea } from '@/components/ui/scroll-area'
import { Textarea } from '@/components/ui/textarea'

//state
const userInput: Ref<string> = ref('')
const feedbackList: Ref<string[]> = ref([])

// create a function to return current year
const getCurrentYear = () => new Date().getFullYear()

// actions
const addFeedbackItem = () => {
  if (!userInput.value || userInput.value.trim() === '') {
    return toast('Please enter feedback to add!')
  }
  feedbackList.value.push(userInput.value)
  userInput.value = ''
  toast('Feedback added!')
}

const deleteFeedbackItem = (str: string) => {
  feedbackList.value = feedbackList.value.filter((item) => item !== str)
  toast('Feedback deleted!')
}

const copyFeedbackToClipboard = (str: string) => {
  const feedback = feedbackList.value.find((item) => item === str)

  if (!feedback) return
  navigator.clipboard.writeText(feedback)
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
  feedbackList.value = feedback ? JSON.parse(feedback) : []
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
        v-for="feedback of feedbackList"
        :key="feedback"
        :feedback="feedback"
        @copy="copyFeedbackToClipboard(feedback)"
        @delete="deleteFeedbackItem(feedback)"
      />
    </ScrollArea>
  </main>
  <footer class="py-4">
    <p class="text-center text-xs text-slate-400">
      © {{ getCurrentYear() }} by Matthew Littrell. Repo available on
      <a
        href="https://github.com/digidevguy/feedback-hub"
        target="_blank"
        class="text-emerald-400 hover:underline"
        >Github</a
      >
    </p>
  </footer>
  <Toaster position="bottom-center" theme="dark" />
</template>

<style scoped>
@media (min-width: 1024px) {
  header {
    padding-right: calc(var(--section-gap) / 2);
  }
}
</style>
