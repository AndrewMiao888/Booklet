<script setup lang="ts">
import { chapters, pages } from '~/data/guide'

const emit = defineEmits<{ sources: [] }>()

const spreadIndex = ref(0)
const savedSpread = ref<number | null>(null)
const showNotes = ref(false)
const toastMessage = ref('')
const currentPages = computed(() => pages.slice(spreadIndex.value * 2, spreadIndex.value * 2 + 2))
const firstPageNumber = computed(() => spreadIndex.value * 2 + 1)
const lastPageNumber = computed(() => Math.min(firstPageNumber.value + 1, pages.length))
const currentChapter = computed(() => [...chapters].reverse().find(chapter => firstPageNumber.value - 1 >= chapter.start) ?? { start: 0, label: 'A small beginning' })
const chapterLinks = computed(() => chapters.map((chapter, index) => ({
  ...chapter,
  spread: Math.floor(chapter.start / 2),
  active: chapter.start <= firstPageNumber.value - 1 && firstPageNumber.value - 1 < (chapters[index + 1]?.start ?? pages.length),
})))
const progress = computed(() => `${(lastPageNumber.value / pages.length) * 100}%`)
const isSaved = computed(() => savedSpread.value === spreadIndex.value)

let toastTimer: ReturnType<typeof setTimeout> | undefined

function goToSpread(index: number, scrollToBook = false) {
  spreadIndex.value = Math.max(0, Math.min(Math.ceil(pages.length / 2) - 1, index))
  if (scrollToBook) {
    document.querySelector('#book')?.scrollIntoView({ behavior: 'smooth', block: 'center' })
  }
}

function showToast(message: string) {
  toastMessage.value = message
  if (toastTimer) clearTimeout(toastTimer)
  toastTimer = setTimeout(() => { toastMessage.value = '' }, 2200)
}

function savePlace() {
  savedSpread.value = spreadIndex.value
  try {
    window.localStorage.setItem('ocean-bookmark', String(spreadIndex.value))
    showToast('Your place is saved on this device.')
  } catch {
    showToast('Your place is saved for this session.')
  }
}

function handleKeydown(event: KeyboardEvent) {
  const target = event.target
  if (!(target instanceof HTMLElement) || target.matches('input, textarea, select, button, [contenteditable="true"]')) return
  if (event.key === 'ArrowRight') goToSpread(spreadIndex.value + 1)
  if (event.key === 'ArrowLeft') goToSpread(spreadIndex.value - 1)
}

onMounted(() => {
  try {
    const stored = window.localStorage.getItem('ocean-bookmark')
    if (stored !== null) {
      const parsed = Number(stored)
      if (Number.isInteger(parsed) && parsed >= 0 && parsed < Math.ceil(pages.length / 2)) {
        savedSpread.value = parsed
        spreadIndex.value = parsed
      }
    }
  } catch {
    // The reader remains usable if browser storage is unavailable.
  }
  window.addEventListener('keydown', handleKeydown)
})

onBeforeUnmount(() => {
  window.removeEventListener('keydown', handleKeydown)
  if (toastTimer) clearTimeout(toastTimer)
})
</script>

<template>
  <section id="book" class="book-section mx-auto w-full max-w-7xl px-4 py-12 sm:px-8" aria-labelledby="book-title">
    <div class="book-heading flex items-end justify-between gap-4">
      <div>
        <p class="eyebrow"><span class="eyebrow-dot" /> YOUR FIELD GUIDE <span class="eyebrow-number">32 PAGES</span></p>
        <h2 id="book-title" class="font-display">Turn the page.</h2>
      </div>
      <button class="quiet-button shrink-0" type="button" @click="goToSpread(0, true)">← Back to the cover</button>
    </div>

    <div class="reader-layout grid grid-cols-1 gap-6 lg:grid-cols-[195px_minmax(0,1fr)] lg:gap-9">
      <aside class="contents-panel" aria-label="Book contents">
        <p class="mono-label">IN THIS LITTLE BOOK</p>
        <nav class="chapter-nav" aria-label="Chapters">
          <button v-for="(chapter, index) in chapterLinks" :key="chapter.label" class="chapter-link" :class="{ active: chapter.active }" type="button" @click="goToSpread(chapter.spread, true)">
            <span>{{ String(index + 1).padStart(2, '0') }}</span>
            <span>{{ chapter.label }}</span>
          </button>
        </nav>
        <div v-if="savedSpread !== null" class="saved-note">↗ Your last page is saved on this device.</div>
      </aside>

      <div class="reader-main min-w-0">
        <div class="book-toolbar flex items-center justify-between">
          <span class="mono-label" aria-live="polite">{{ String(firstPageNumber).padStart(2, '0') }} — {{ String(lastPageNumber).padStart(2, '0') }} / {{ pages.length }}</span>
          <div class="toolbar-actions flex items-center gap-3">
            <button class="toolbar-button" type="button" :aria-pressed="isSaved" :title="isSaved ? 'Place saved' : 'Save your place'" @click="savePlace">{{ isSaved ? '♥' : '♡' }} <span>{{ isSaved ? 'Saved' : 'Save place' }}</span></button>
            <button class="toolbar-button" type="button" :aria-expanded="showNotes" @click="showNotes = !showNotes">◎ <span>Field notes</span></button>
          </div>
        </div>

        <div class="book-shell">
          <div class="spread" :data-first-spread="spreadIndex === 0">
            <BookPage v-for="(page, index) in currentPages" :key="firstPageNumber + index" :page="page" :page-number="firstPageNumber + index" :side="index === 0 ? 'left' : 'right'" @sources="emit('sources')" />
          </div>
        </div>

        <div class="reader-controls grid grid-cols-[36px_minmax(0,1fr)_36px] items-center gap-3">
          <button class="round-button" type="button" aria-label="Previous pages" :disabled="spreadIndex === 0" @click="goToSpread(spreadIndex - 1)">←</button>
          <div class="progress-wrap min-w-0">
            <div class="progress-label flex justify-between gap-2">
              <span>PAGES {{ String(firstPageNumber).padStart(2, '0') }}–{{ String(lastPageNumber).padStart(2, '0') }}</span>
              <span>{{ currentChapter.label.toUpperCase() }}</span>
            </div>
            <div class="progress-track"><div :style="{ width: progress }" /></div>
          </div>
          <button class="round-button round-next" type="button" aria-label="Next pages" :disabled="spreadIndex >= Math.ceil(pages.length / 2) - 1" @click="goToSpread(spreadIndex + 1)">→</button>
        </div>
        <div class="key-hint">TIP <span>use ← and → to turn pages</span> <span class="hide-mobile">· click a chapter to jump</span></div>

        <aside v-if="showNotes" class="notes-panel items-start" aria-label="A note on the numbers">
          <span class="notes-icon">✳</span>
          <div>
            <strong>A note on the numbers</strong>
            <p>Microplastic counts vary with the sample, the method and how small a particle a lab can see. We label estimates, show where each number comes from and link to the original research. Counts are not a health-risk score.</p>
          </div>
          <button class="quiet-button ml-auto" type="button" aria-label="Close field notes" @click="showNotes = false">×</button>
        </aside>
      </div>
    </div>

    <footer class="book-footer flex items-center justify-between gap-4">
      <span>FIELD NOTES / 001 <span>·</span> SMALL THINGS, BIG OCEAN</span>
      <a href="#sources" @click.prevent="emit('sources')">Sources &amp; further reading <span aria-hidden="true">↗</span></a>
    </footer>
    <div v-if="toastMessage" class="toast" role="status" aria-live="polite">{{ toastMessage }}</div>
  </section>
</template>
