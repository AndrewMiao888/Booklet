<script setup lang="ts">
import type { InteractionKind } from '~/data/guide'

const props = defineProps<{ kind: InteractionKind }>()

const scale = ref(30)
const volumeMl = ref(1000)
const quizAnswer = ref<'measured' | 'estimate' | null>(null)
const gyreRevealed = ref(false)
const selectedSwap = ref<'drink' | 'shopping' | 'cleaning' | null>(null)
const selectedHabit = ref<'wear' | 'wash' | 'mend' | null>(null)
const selectedPlace = ref<'Australia' | 'elsewhere' | null>(null)
const checks = ref<string[]>([])
const storageReady = ref(false)

const scaleLabel = computed(() => scale.value < 20 ? 'VISIBLE FLECK' : scale.value < 45 ? 'MICROPLASTIC' : scale.value < 72 ? 'FINE MICROPLASTIC' : 'NANOPLASTIC')
const particleCount = computed(() => Math.round(240_000 * volumeMl.value / 1_000).toLocaleString())
const swapTips = {
  drink: 'Keep a bottle you already own near your keys. Refill at home, a water station or a café that welcomes reusables.',
  shopping: 'Start with a sturdy bag you already have. Look for loose produce and refill options at local markets or bulk shops.',
  cleaning: 'Search for a local refill shop or supermarket refill aisle. Bring a clean container and check the store’s policy.',
}
const swapChoices = [
  { value: 'drink' as const, label: 'Drinks on the go' },
  { value: 'shopping' as const, label: 'Weekly shopping' },
  { value: 'cleaning' as const, label: 'Household refills' },
]
const habitTips = {
  wear: 'A second wear between washes can save water, energy and some fibre shedding.',
  wash: 'A gentler full load can reduce friction and avoid unnecessary wash cycles.',
  mend: 'A quick repair can keep useful clothing in service for longer.',
}
const habitChoices = [
  { value: 'wear' as const, label: 'Wear it a few more times' },
  { value: 'wash' as const, label: 'Wash full loads gently' },
  { value: 'mend' as const, label: 'Mend before replacing' },
]

onMounted(() => {
  if (props.kind === 'checklist') {
    try {
      const stored = window.localStorage.getItem('ocean-week-plan')
      checks.value = stored ? JSON.parse(stored) as string[] : []
    } catch {
      checks.value = []
    }
  }
  storageReady.value = true
})

watch(checks, (value) => {
  if (props.kind === 'checklist' && storageReady.value) {
    window.localStorage.setItem('ocean-week-plan', JSON.stringify(value))
  }
}, { deep: true })
</script>

<template>
  <div v-if="kind === 'scale'" class="interaction-box">
    <div class="interaction-label">✳ MOVE THE SCALE</div>
    <p>Drag to see how the size label changes. A sand grain is visible; a nanoplastic is much smaller than the smallest dot.</p>
    <input v-model.number="scale" class="scale-slider w-full accent-sea-deep" type="range" min="0" max="100" aria-label="Explore particle scale">
    <div class="scale-meter" />
    <div class="range-line"><span>VISIBLE</span><span class="range-value">{{ scaleLabel }}</span></div>
  </div>

  <div v-else-if="kind === 'bottle'" class="interaction-box">
    <div class="interaction-label">✳ TRY THE BOTTLE CALCULATOR</div>
    <p>Apply the study’s average of about 240,000 particles per litre. This is an illustration, not a test of your bottle.</p>
    <div class="range-line">
      <input v-model.number="volumeMl" class="bottle-slider w-full accent-sea-deep" type="range" min="250" max="2000" step="250" aria-label="Choose bottle volume in millilitres">
      <span class="range-value min-w-24 text-right">{{ volumeMl.toLocaleString() }} mL<br>≈ {{ particleCount }}</span>
    </div>
    <div class="interaction-feedback">Particles counted included nano- and microplastics; most were nanoplastics.</div>
  </div>

  <div v-else-if="kind === 'credit'" class="interaction-box">
    <div class="interaction-label">✳ QUICK MYTH CHECK</div>
    <p>What does “a credit card a week” actually represent?</p>
    <div class="choice-list">
      <button class="choice-button" :class="{ selected: quizAnswer === 'measured' }" type="button" @click="quizAnswer = 'measured'">A direct weekly measurement</button>
      <button class="choice-button" :class="{ selected: quizAnswer === 'estimate' }" type="button" @click="quizAnswer = 'estimate'">A broad estimate with major uncertainty</button>
    </div>
    <div class="interaction-feedback" aria-live="polite">{{ quizAnswer === 'estimate' ? 'That’s it. The 2019 card comparison came from an uncertain model, not a weekly body measurement.' : quizAnswer === 'measured' ? 'Not quite. The headline came from a broad model estimate, not a direct weekly measurement.' : 'Choose a card to reveal the answer.' }}</div>
  </div>

  <div v-else-if="kind === 'gyre'" class="interaction-box">
    <div class="interaction-label">✳ TAP THE CURRENT</div>
    <p>Why does debris gather in a gyre?</p>
    <button class="flip-card w-full" type="button" :aria-expanded="gyreRevealed" @click="gyreRevealed = !gyreRevealed">
      <span class="flip-card-inner">{{ gyreRevealed ? 'Gyres are large, slow-turning ocean current systems. Floating debris can collect inside them — mixed across a wide area, not stacked into an island.' : 'Tap to reveal the slow-turning current clue ↗' }}</span>
    </button>
  </div>

  <div v-else-if="kind === 'swap'" class="interaction-box">
    <div class="interaction-label">✳ FIND A FIRST SWAP</div>
    <p>Pick where you want to start. Try nearby refill and second-hand options.</p>
    <div class="choice-list">
      <button v-for="option in swapChoices" :key="option.value" class="choice-button" :class="{ selected: selectedSwap === option.value }" type="button" @click="selectedSwap = option.value">{{ option.label }}</button>
    </div>
    <div class="interaction-feedback" aria-live="polite">{{ selectedSwap ? swapTips[selectedSwap] : 'Your easiest next step goes here.' }}</div>
  </div>

  <div v-else-if="kind === 'laundry'" class="interaction-box">
    <div class="interaction-label">✳ PICK ONE LOW-FUSS HABIT</div>
    <div class="choice-list">
      <button v-for="option in habitChoices" :key="option.value" class="choice-button" :class="{ selected: selectedHabit === option.value }" type="button" @click="selectedHabit = option.value">{{ option.label }}</button>
    </div>
    <div class="interaction-feedback" aria-live="polite">{{ selectedHabit ? habitTips[selectedHabit] : 'No special kit needed to start.' }}</div>
  </div>

  <div v-else-if="kind === 'recycle'" class="interaction-box">
    <div class="interaction-label">✳ WHERE ARE YOU RECYCLING?</div>
    <p>Choose the setting to reveal the best next step.</p>
    <div class="choice-list">
      <button class="choice-button" :class="{ selected: selectedPlace === 'Australia' }" type="button" @click="selectedPlace = 'Australia'">Australia</button>
      <button class="choice-button" :class="{ selected: selectedPlace === 'elsewhere' }" type="button" @click="selectedPlace = 'elsewhere'">Somewhere else</button>
    </div>
    <div class="interaction-feedback" aria-live="polite">{{ selectedPlace === 'Australia' ? 'Search Recycling Near You for council-specific bin and drop-off rules.' : selectedPlace === 'elsewhere' ? 'Look up your local council or waste authority. Collection rules vary by place.' : 'Use your local collection rules as the source of truth.' }}</div>
  </div>

  <div v-else-if="kind === 'checklist'" class="interaction-box">
    <div class="interaction-label">✳ BUILD YOUR TINY WEEK PLAN</div>
    <div class="checklist">
      <label v-for="item in ['Carry a bottle', 'Take a reusable bag', 'Refill a container', 'Check local recycling']" :key="item" class="check-row">
        <input v-model="checks" class="accent-sea-deep" type="checkbox" :value="item"> {{ item }}
      </label>
    </div>
    <div class="interaction-feedback" aria-live="polite">{{ checks.length }} small action{{ checks.length === 1 ? '' : 's' }} picked. Keep your plan simple and doable.</div>
  </div>
</template>
