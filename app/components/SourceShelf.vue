<script setup lang="ts">
import { imageSources, sources } from '~/data/guide'

defineEmits<{ back: [] }>()
</script>

<template>
  <section id="sources" class="sources-section mx-auto min-h-[80vh] max-w-5xl px-6 py-16" aria-labelledby="sources-title">
    <p class="eyebrow"><span class="eyebrow-dot" /> KEEP FOLLOWING THE THREAD</p>
    <h2 id="sources-title" class="font-display">Sources &amp; further reading</h2>
    <p class="source-intro">A pocket guide should make it easy to check the footnotes. These links lead to the studies and public-health guidance used in the book.</p>

    <div class="source-list grid grid-cols-1 gap-3 md:grid-cols-2">
      <article v-for="source in sources" :key="source.url" class="source-card rounded-sm border border-stone-200 bg-white/70 p-5">
        <div class="source-type">{{ source.type }}</div>
        <h3 class="font-display">{{ source.title }}</h3>
        <p>{{ source.note }}</p>
        <a :href="source.url" target="_blank" rel="noreferrer">{{ source.label }}</a>
      </article>
    </div>

    <h3 class="media-source-heading font-display">Image credits</h3>
    <p class="source-intro">The booklet uses real photographs and maps with visible credit beside each image. These are the original image pages and license notes.</p>
    <div class="media-source-list grid grid-cols-1 gap-3 md:grid-cols-3">
      <article v-for="image in imageSources" :key="image.key" class="media-source-card">
        <img :src="image.src" :alt="image.alt" loading="lazy">
        <div>
          <span>{{ image.license }}</span>
          <h4>{{ image.title }}</h4>
          <p>{{ image.note }}</p>
          <a :href="image.sourceUrl" target="_blank" rel="noreferrer">{{ image.credit }} ↗</a>
        </div>
      </article>
    </div>

    <button class="button button-light" type="button" @click="$emit('back')">← Back to the book</button>
  </section>
</template>
