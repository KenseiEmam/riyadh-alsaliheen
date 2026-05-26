<template>
  <div
    v-show="props"
    class="carousel__item h-[30vh] w-full relative"
  >
    <img
      class="absolute w-full h-full grayscale-60 object-cover "
      :src="image"
    />

    <div
      class="absolute w-full h-full bg-gradient-to-t from-primary-background to-secondary-background/40"
    ></div>

    <div
      class="flex flex-col w-full h-full relative items-center justify-center md:max-w-1/3 text-center mx-auto px-8"
    >
      <h2 class="title-small text-primary/80">
        {{ title }}
      </h2>

      <h2 class="title-medium text-primary/90">
        {{ nextPrayerArabic }}
      </h2>

      <p class="header-medium text-primary/80">
        بعد
      </p>

      <p class="text-primary title-large font-bold mt-8">
        {{ countdown }}
      </p>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted, computed } from 'vue'


const props = defineProps(['title', 'image'])



const prayerTimes = ref<Record<string, string>>({})
const nextPrayer = ref('')
const countdown = ref('')

let interval: ReturnType<typeof setInterval>

const prayerNamesArabic: Record<string, string> = {
  Fajr: 'الفجر',
  Dhuhr: 'الظهر',
  Asr: 'العصر',
  Maghrib: 'المغرب',
  Isha: 'العشاء',
}

const nextPrayerArabic = computed(() => {
  return prayerNamesArabic[nextPrayer.value] || ''
})

const fetchPrayerTimes = async () => {
  try {
    const response = await fetch(
      'https://api.aladhan.com/v1/timingsByCity?city=Alexandria&country=Egypt&method=5'
    )

    const data = await response.json()

    prayerTimes.value = data.data.timings

    calculateNextPrayer()
  } catch (error) {
    console.error(error)
  }
}

const calculateNextPrayer = () => {
  const now = new Date()

  const prayers = ['Fajr', 'Dhuhr', 'Asr', 'Maghrib', 'Isha']

  for (const prayer of prayers) {
    if(!prayerTimes.value[prayer])
    return
    const [hours, minutes] = prayerTimes.value[prayer]
      .split(':')
      .map(Number)

    const prayerDate = new Date()

    prayerDate.setHours(hours || 0)
    prayerDate.setMinutes(minutes || 0)
    prayerDate.setSeconds(0)

    if (prayerDate > now) {
      nextPrayer.value = prayer

      startCountdown(prayerDate)

      return
    }
  }
  
  // fallback to tomorrow fajr
  const [hours, minutes] = prayerTimes.value['Fajr'] || "00:00:00"
    .split(':')
    .map(Number)

  const fajrTomorrow = new Date()
  if(!hours || !minutes)
  return
  fajrTomorrow.setDate(fajrTomorrow.getDate() + 1)
  fajrTomorrow.setHours(parseInt(hours?.toString()))
  fajrTomorrow.setMinutes(parseInt(minutes?.toString()))
  fajrTomorrow.setSeconds(0)

  nextPrayer.value = 'Fajr'

  startCountdown(fajrTomorrow)
}

const startCountdown = (target: Date) => {
  clearInterval(interval)

  const update = () => {
    const now = new Date().getTime()

    const distance = target.getTime() - now

    if (distance <= 0) {
      calculateNextPrayer()
      return
    }

    const hours = Math.floor(distance / (1000 * 60 * 60))
    const minutes = Math.floor(
      (distance % (1000 * 60 * 60)) / (1000 * 60)
    )
    const seconds = Math.floor((distance % (1000 * 60)) / 1000)

    countdown.value = `${hours
      .toString()
      .padStart(2, '0')}:${minutes
      .toString()
      .padStart(2, '0')}:${seconds
      .toString()
      .padStart(2, '0')}`
  }

  update()

  interval = setInterval(update, 1000)
}

onMounted(async () => {
  await fetchPrayerTimes()
})

onUnmounted(() => {
  clearInterval(interval)
})
</script>

<style>
.carousel {
  --vc-nav-background: rgba(0, 0, 0, 0.3);
  --vc-nav-color: white;
  --vc-nav-color-hover: #e5e5e5;
  --vc-nav-border-radius: 50%;
  --vc-nav-width: 40px;
  --vc-nav-height: 40px;
  --vc-pgn-background-color: white;
  --vc-pgn-active-color: rgb(178, 180, 162);
  --vc-pgn-border-radius: 5px;
  --vc-pgn-height: 10px;
  --vc-pgn-width: 10px;
}
</style>