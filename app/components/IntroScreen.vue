<script setup lang="ts">
import { imageSources } from '~/data/guide'

const emit = defineEmits<{ open: [] }>()
const heroImage = imageSources.find(image => image.key === 'beachTrash') ?? imageSources[0]
</script>

<template>
  <section class="intro-screen grid grid-cols-1 items-center gap-8 px-6 py-10 lg:grid-cols-2 lg:gap-20" aria-labelledby="intro-title">
    <div class="hero-art w-full">
      <img v-if="heroImage" class="hero-photo" :src="heroImage.src" :alt="heroImage.alt">
      <a v-if="heroImage" class="hero-source" :href="heroImage.sourceUrl" target="_blank" rel="noreferrer">{{ heroImage.credit }} / {{ heroImage.license }}</a>
      <div class="hero-star star-a">✳</div>
      <div class="hero-star star-b">✴</div>
      <svg class="hero-ocean" viewBox="0 0 900 490" role="presentation" xmlns="http://www.w3.org/2000/svg">
        <path d="M35 265C125 241 179 279 251 267s99-35 177-18 121 40 202 19 141-46 224-30" fill="none" stroke="#c9dfe7" stroke-width="104" stroke-linecap="round" opacity=".9" />
        <path d="M12 314c94-21 158 20 244 4s114-37 185-20 118 39 207 19 146-45 247-27" fill="none" stroke="#d7e8ec" stroke-width="71" stroke-linecap="round" />
        <path d="M25 350c78-10 132 21 219 7s114-29 180-15 120 37 206 17 145-38 250-17" fill="none" stroke="#e7f0f2" stroke-width="40" stroke-linecap="round" />
        <ellipse cx="447" cy="228" rx="153" ry="52" fill="#f3d998" transform="rotate(-10 447 228)" />
        <path d="M344 226c49-28 153-42 204-8l13 18c-74 19-153 31-213 10z" fill="#d69a68" />
        <rect x="367" y="214" width="140" height="15" rx="7" fill="#ebc98b" transform="rotate(-8 367 214)" />
        <rect x="367" y="237" width="119" height="9" rx="4" fill="#f5e7c8" transform="rotate(-8 367 237)" />
        <path d="M284 203c16-23 38-18 47-3 18 29-34 44-28 72-39-17-45-42-19-69z" fill="#b7a7cf" />
        <path d="M578 246c5-31 37-46 62-27 22 17 8 43-18 39-20-3-31 8-34 28-15-11-13-25-10-40z" fill="#82a9a2" />
        <circle cx="614" cy="118" r="31" fill="#ead4ab" /><circle cx="614" cy="118" r="21" fill="#f1f7f9" /><circle cx="614" cy="118" r="11" fill="#86aaa2" />
        <path d="M176 179l12-30 12 30 31 12-31 12-12 31-12-31-30-12z" fill="#dc966f" />
        <circle cx="700" cy="315" r="7" fill="#eea16f" /><circle cx="734" cy="283" r="5" fill="#ad97c1" /><circle cx="226" cy="310" r="6" fill="#e3ac6e" />
        <path d="M790 135c17 13 22 34 11 52m10-46c19 15 25 39 13 59m9-49c20 16 27 40 15 60" fill="none" stroke="#81aaa2" stroke-width="6" stroke-linecap="round" />
        <path d="M120 407c37-7 64-7 98 2m459 22c38-11 71-8 105 4" fill="none" stroke="#9abac5" stroke-width="2" stroke-dasharray="3 9" stroke-linecap="round" />
      </svg>
      <span class="hero-caption">A field guide to the little things<br>that travel a very long way.</span>
      <span class="art-index">FIG. 01 <span>·</span> THE LONG WAY ROUND</span>
    </div>

    <div class="intro-copy py-4">
      <p class="eyebrow"><span class="eyebrow-dot" /> AN INTERACTIVE FIELD GUIDE <span class="eyebrow-number">ISSUE Nº 001</span></p>
      <h1 id="intro-title" class="font-display">Small things.<br><em>Big ocean.</em></h1>
      <p class="intro-deck">A curious, practical journey through the plastic we can’t always see — where it goes, what the evidence says, and what we can do next.</p>
      <div class="intro-actions flex flex-wrap items-center gap-4">
        <button class="button button-dark" type="button" @click="emit('open')">Open the field guide <span aria-hidden="true">↗</span></button>
        <span class="read-time">32 pages <span>·</span> about 12 minutes</span>
      </div>
      <div class="intro-foot">
        <span><span class="foot-rule" /> LOOK CLOSER. WASTE LESS.</span>
        <span>MADE FOR THE CURIOUS <span aria-hidden="true">✳</span></span>
      </div>
    </div>
  </section>
</template>
