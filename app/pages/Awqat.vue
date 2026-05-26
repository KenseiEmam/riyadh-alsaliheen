<template>
  <TimeBanner
    title="الصلاة التالية"
    image="https://cdn11.bigcommerce.com/s-przkrobm2w/product_images/uploaded_images/what-time-are-the-5-daily-prayers-in-islam.jpg"
  />

  <section class="container bg-primary-backgroud py-4 md:py-8 2xl:py-12 space-y-6 min-h-max">
    <!-- Intro text (like your mosque description) -->
    <p class="text-body">
      مواقيت الصلاة اليومية في الإسكندرية يتم تحديثها حسب الحسابات الفلكية.
      <br />
      الإقامة تكون بعد الصلاة مباشرة بفاصل ثابت في أغلب الصلوات.
    </p>

    <!-- Prayer list -->
    <div class="space-y-3">
      <div
        v-for="p in prayers"
        :key="p.name"
        class="flex items-center justify-between bg-primary-text/5 rounded-2xl p-4"
      >
        <!-- Left -->
        <div>
          <p class="title-medium">
            {{ p.ar }}
          </p>
          <p class="text-body text-primary-text/60">
            {{ p.name }}
          </p>
        </div>

        <!-- Right -->
        <div class="text-end">
          <p class="header-medium">
            {{ p.time }}
          </p>
          <p class="text-sm text-primary-text/60">
            الإقامة {{ p.iqama }}
          </p>
        </div>
      </div>
    </div>

    <!-- Next prayer card -->
    <div class="bg-primary-text/10 rounded-3xl p-6 text-center space-y-2">
      <p class="text-body text-primary-text/70">
        الصلاة القادمة
      </p>

      <h2 class="title-large">
        {{ nextPrayerArabic }}
      </h2>

      <p class="title-large font-bold">
        {{ countdown }}
      </p>
    </div>
  </section>
</template>

<script setup lang="ts">
import { computed, onMounted, onUnmounted, ref } from 'vue'
import TimeBanner from '~/components/TimeBanner.vue'

type PrayerName = 'Fajr' | 'Dhuhr' | 'Asr' | 'Maghrib' | 'Isha'

const prayerTimes = ref<Record<string, string>>({})
const nextPrayer = ref<PrayerName | ''>('')

const countdown = ref('')

let interval: ReturnType<typeof setInterval>

const prayersList: PrayerName[] = [
  'Fajr',
  'Dhuhr',
  'Asr',
  'Maghrib',
  'Isha'
]

const arabic: Record<PrayerName, string> = {
  Fajr: 'الفجر',
  Dhuhr: 'الظهر',
  Asr: 'العصر',
  Maghrib: 'المغرب',
  Isha: 'العشاء'
}

const iqamaOffset: Record<PrayerName, number> = {
  Fajr: 20,
  Dhuhr: 20,
  Asr: 20,
  Maghrib: 10,
  Isha: 20
}

/* ---------- computed UI list ---------- */
const prayers = computed(() => {
  return prayersList.map((p) => {
    const time = prayerTimes.value[p]

    return {
      name: p,
      ar: arabic[p],
      time,
      iqama: getIqama(time??'', iqamaOffset[p])
    }
  })
})

const nextPrayerArabic = computed(() => {
  return nextPrayer.value ? arabic[nextPrayer.value] : ''
})

/* ---------- API ---------- */
const fetchTimes = async () => {
  const res = await fetch(
    'https://api.aladhan.com/v1/timingsByCity?city=Alexandria&country=Egypt&method=5'
  )

  const data = await res.json()

  prayerTimes.value = data.data.timings

  calculateNext()
}

/* ---------- logic ---------- */
const calculateNext = () => {
  const now = new Date()

  for (const p of prayersList) {
    const t = prayerTimes.value[p]
    if (!t) return

    const [h, m] = t.split(':').map(Number)

    const d = new Date()
    d.setHours(h??0, m, 0, 0)

    if (d > now) {
      nextPrayer.value = p
      startCountdown(d)
      return
    }
  }

  // fallback
  const [h, m] = prayerTimes.value.Fajr!.split(':').map(Number)

  const d = new Date()
  d.setDate(d.getDate() + 1)
  d.setHours(h??0, m, 0, 0)

  nextPrayer.value = 'Fajr'
  startCountdown(d)
}

const startCountdown = (target: Date) => {
  clearInterval(interval)

  const tick = () => {
    const now = Date.now()
    const diff = target.getTime() - now

    if (diff <= 0) {
      calculateNext()
      return
    }

    const h = Math.floor(diff / 3.6e6)
    const m = Math.floor((diff % 3.6e6) / 6e4)
    const s = Math.floor((diff % 6e4) / 1000)

    countdown.value = `${h.toString().padStart(2, '0')}:${m
      .toString()
      .padStart(2, '0')}:${s.toString().padStart(2, '0')}`
  }

  tick()
  interval = setInterval(tick, 1000)
}

/* ---------- iqama ---------- */
const getIqama = (time: string, offset: number) => {
  if (!time) return '--:--'

  const [h, m] = time.split(':').map(Number)
    if(!h || !m)
    return
  const d = new Date()
  d.setHours(h, m + offset, 0, 0)

  return d.toLocaleTimeString('en-GB', {
    hour: '2-digit',
    minute: '2-digit'
  })
}

onMounted(fetchTimes)
onUnmounted(() => clearInterval(interval))
</script>