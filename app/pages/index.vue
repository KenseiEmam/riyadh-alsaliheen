<template>
    <TimeBanner title="الصلاة التالية" :image="mainpicture"/>
<section class="container bg-primary-backgroud py-4 md:py-8 2xl:py-12">
  <div class=" mx-auto">
    <h2 class="title-medium mb-4">
      عن المسجد
    </h2>

    <p class="text-body leading-relaxed whitespace-pre-line text-primary-text/80">
      يقع مسجد رياض في شارع طببة الصالحين وسط حي سبورتنج بالإسكندرية، قريباً من شارع دلتا.

      يتميز المسجد بمصلى داخلي مكيف يسع عدداً كبيراً من المصلين ويوفر أجواء هادئة ومريحة للعبادة، مع مرافق متكاملة تشمل أماكن مخصصة للوضوء (مغاسل/أحواض وضوء) بالإضافة إلى دورات مياه مجهزة لخدمة الزوار.

      تتوفر بالمكان مصاحف متعددة للقراءة داخل المسجد، مما يتيح للمصلين فرصة تلاوة القرآن في أي وقت.

      كما يضم المسجد ساحة خارجية مفروشة بالسجاد تُستخدم خصوصاً في صلاة الجمعة عند زيادة أعداد المصلين، وتوفر مساحة إضافية منظمة للصلاة.
    </p>
  </div>
</section>
    
  <section class="container border-t border-teritiary/30  bg-primary-backgroud py-4 md:py-8 2xl:py-12 space-y-6 min-h-max">

    <h3 class="header-large text-center">
      مواقيت الصلاة
      
    </h3>

    <!-- Prayer list -->
    <div class="grid md:grid-cols-3 2xl:grid-cols-5  gap-3">
      <div
        v-for="p in prayers"
        :key="p.name"
        class="flex flex-col items-center text-center justify-between bg-primary-text/5 rounded-2xl p-4 py-8"
      >
        <!-- Left -->
        <div>
          <p class="title-small">
            {{ p.ar }}
          </p>
          <p class="text-body text-primary-text/60">
            {{ p.name }}
          </p>
        </div>

        <!-- Right -->
        <div class="">
          <p class="header-large">
            {{ p.time }}
          </p>
        </div>
      </div>
    </div>
  </section>
</template>

<script setup lang="ts">
import { computed, onMounted, onUnmounted, ref } from 'vue'
import TimeBanner from '~/components/TimeBanner.vue'
import mainpicture from '~/assets/images/WhatsApp Image 2026-05-26 at 2.18.28 PM.jpeg'
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