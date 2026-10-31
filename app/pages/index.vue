<script setup lang="ts">
type View = 'home' | 'book' | 'sources'

const currentView = ref<View>('home')

useSeoMeta({
  title: 'Small Things, Big Ocean — an interactive field guide',
  description: 'A curious, practical field guide to plastic pollution, microplastics, the Great Pacific Garbage Patch and everyday changes.',
  themeColor: '#f4f0e6',
})

useHead({
  link: [
    { rel: 'preconnect', href: 'https://fonts.googleapis.com' },
    { rel: 'preconnect', href: 'https://fonts.gstatic.com', crossorigin: 'anonymous' },
  ],
})

function setView(view: View) {
  currentView.value = view
  if (import.meta.client) {
    const hash = view === 'home' ? '' : `#${view}`
    window.history.replaceState(null, '', `${window.location.pathname}${hash}`)
    window.scrollTo({ top: 0, behavior: 'smooth' })
  }
}

onMounted(() => {
  if (window.location.hash === '#book') currentView.value = 'book'
  if (window.location.hash === '#sources') currentView.value = 'sources'
})
</script>

<template>
  <div class="min-h-screen bg-paper text-ink">
    <header class="site-header flex items-center justify-between px-6 py-5 sm:px-10 lg:px-20">
      <NuxtLink class="wordmark" to="/" aria-label="Small Things, Big Ocean — home" @click="setView('home')">
        <span class="wordmark-mark" aria-hidden="true">✳</span> FIELD NOTES <span class="wordmark-slash">/</span> 001
      </NuxtLink>
      <a v-if="currentView === 'home'" class="edition" href="#book" @click.prevent="setView('book')">A pocket guide to plastics <span aria-hidden="true">↗</span></a>
      <button v-else class="quiet-button" type="button" @click="setView('home')">← Back to the cover</button>
    </header>

    <main id="main-content">
      <Transition name="view-fade" mode="out-in">
        <IntroScreen v-if="currentView === 'home'" key="home" @open="setView('book')" />
        <BookReader v-else-if="currentView === 'book'" key="book" @sources="setView('sources')" />
        <SourceShelf v-else key="sources" @back="setView('book')" />
      </Transition>
    </main>

    <footer class="site-footer flex items-center justify-between gap-3 px-6 py-5 sm:px-10 lg:px-20">
      <span>SMALL THINGS, BIG OCEAN <span>·</span> AN INTERACTIVE FIELD GUIDE</span>
      <button v-if="currentView !== 'home'" class="text-sea-deep hover:underline" type="button" @click="setView('home')">BACK TO THE TOP ↑</button>
      <a v-else href="#home">BACK TO THE TOP ↑</a>
    </footer>
  </div>
</template>

<style scoped>
.view-fade-enter-active,
.view-fade-leave-active {
  transition: opacity 160ms ease;
}

.view-fade-enter-from,
.view-fade-leave-to {
  opacity: 0;
}

@media (prefers-reduced-motion: reduce) {
  .view-fade-enter-active,
  .view-fade-leave-active {
    transition: none;
  }
}
</style>
