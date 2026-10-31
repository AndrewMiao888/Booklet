<script setup lang="ts">
import { artSvg, imageSources, sources, type GuidePage } from '~/data/guide'

const props = defineProps<{
  page: GuidePage
  pageNumber: number
  side: 'left' | 'right'
}>()

const emit = defineEmits<{ sources: [] }>()

const titleText = computed(() => props.page.title.replace(/<[^>]*>/g, ''))
const pageMedia = computed(() => imageSources.find(image => image.key === props.page.media))
const pageClass = computed(() => [
  'book-page',
  props.side === 'right' && 'right-page',
  props.page.cover && 'cover-page',
  props.page.back && 'blank-page',
])
const formattedCallout = computed(() => (props.page.callout ?? '').replace(
  /\[([^\]]+)\]\((https?:\/\/[^)]+)\)/g,
  '<a href="$2" target="_blank" rel="noreferrer">$1</a>',
))
const pageReferences = computed(() => (props.page.refs ?? []).flatMap((index, citationIndex) => {
  const source = sources[index]
  return source ? [{ source, citationIndex }] : []
}))
</script>

<template>
  <article :class="pageClass" :aria-label="`Page ${pageNumber}: ${titleText}`">
    <div class="page-kicker flex items-center justify-between gap-3">
      <span>{{ page.cover ? 'FIELD GUIDE / Nº 001' : page.back ? 'BACK COVER' : `${String(pageNumber - 1).padStart(2, '0')} / ${page.kicker.split(' / ').slice(1).join(' / ')}` }}</span>
      <span class="page-number">{{ String(pageNumber).padStart(2, '0') }} / 32</span>
    </div>

    <h2 class="page-title font-display" v-html="page.title" />
    <div class="page-copy" v-html="page.body" />
    <div v-if="page.callout" class="page-callout" v-html="formattedCallout" />

    <InteractionPanel v-if="page.interaction" :kind="page.interaction" />

    <div v-if="pageReferences.length" class="inline-cites">
      SOURCE{{ pageReferences.length > 1 ? 'S' : '' }}:
      <template v-for="reference in pageReferences" :key="reference.source.url">
        <span v-if="reference.citationIndex" aria-hidden="true"> · </span>
        <a :href="reference.source.url" target="_blank" rel="noreferrer">
          {{ reference.source.type.split(' · ')[0] }}{{ pageReferences.length > 1 ? ` ${reference.citationIndex + 1}` : '' }} ↗
        </a>
      </template>
    </div>

    <div class="page-visual mt-auto">
      <figure v-if="pageMedia" class="source-photo" :class="{ 'source-photo--cover': page.cover || page.back }">
        <img :src="pageMedia.src" :alt="pageMedia.alt" loading="lazy">
        <figcaption>
          <span>{{ pageMedia.title }}</span>
          <a :href="pageMedia.sourceUrl" target="_blank" rel="noreferrer">{{ pageMedia.credit }} / {{ pageMedia.license }}</a>
        </figcaption>
      </figure>
      <div v-else-if="page.cover || page.back" class="cover-graphic" v-html="artSvg(page.art)" />
      <template v-else>
        <div class="max-w-full" v-html="artSvg(page.art)" />
        <span class="illustration-caption">FIELD SKETCH / {{ String(pageNumber).padStart(2, '0') }}</span>
      </template>
    </div>

    <footer class="page-footer flex items-center justify-between">
      <span>SMALL THINGS, BIG OCEAN</span>
      <span>{{ String(pageNumber).padStart(2, '0') }}</span>
    </footer>
  </article>
</template>
