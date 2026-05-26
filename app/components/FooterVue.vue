<template>
  <footer
    class="text-primary relative w-full bg-primary-background border-t border-teritiary/40 pt-12 pb-8"
  >
    <div class="container mx-auto px-6 grid grid-cols-1 md:grid-cols-4 gap-8">
      <!-- Company Info -->
      <div class="space-y-4">
        <h2 class="text-2xl font-bold">مسجد رياض الصالحين</h2>
        <p class="text-secondary-text text-sm">
          تقبل الله صلاتكم
        </p>
        <p class="text-secondary-text text-sm">
          Tiba, Al Ibrahimeyah Bahri WA Sidi Gaber, Bab Shar', Alexandria Governorate 
        </p>
        <a
          href="tel:+200000000000"
          class="block text-primary font-bold text-sm hover:text-primary transition"
        >
          +20 000 000 0000
        </a>
        <a
          href="mailto:test@test.com"
          class="block text-primary font-bold text-sm hover:text-primary transition"
        >
          masjid-test@test.com
        </a>
      </div>

      <!-- Quick Links -->
      <div>
        <h3 class="uppercase text-sm font-bold mb-4">الروابط</h3>
        <ul class="space-y-2 text-secondary-text text-sm">
          <li>
            <RouterLink to="/" class="hover:text-primary transition">الصفحة الرئيسية</RouterLink>
          </li>
          <li>
            <RouterLink to="/Awqat" class="hover:text-primary transition">اوقات الصلاه</RouterLink>
          </li>
          <li>
            <RouterLink to="/Manshurat" class="hover:text-primary transition">المنشورات</RouterLink>
          </li>
          <li>
            <RouterLink to="/Istifsar" class="hover:text-primary transition">الاستفسارات</RouterLink>
          </li>

        </ul>
      </div>

  

      <!-- Newsletter -->
      <div class="space-y-4">
        <h3 class="text-lg font-bold mb-2">حمل المنشورات</h3>
        <div class="flex flex-col gap-2">
          <a v-for="manshur in manshurat"  :href="manshur.to" target="_blank" rel="noopener" class="text-secondary-text text-sm hover:text-primary transition">{{manshur.label}}</a>
          </div>
      </div>
    </div>

    <div class="pt-12 mt-12 text-center text-secondary-text text-sm border-t border-teritiary/40 container">
      2026, Riyadh Al-Saliheen Mosque, Alexandria, Egypt
    </div>
  </footer>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const email = ref<string>('')
const message = ref<string>('')
const manshurat = [
  {
    label: 'خطبة الجمعة - نموذج 1 (PDF)',
    to: '#'
  },
  {
    label: 'دروس المسجد الأسبوعية (PDF)',
    to: '#'
  },
  {
    label: 'كتيب آداب المسجد (PDF)',
    to: '#'
  },
  {
    label: 'أذكار بعد الصلاة (PDF)',
    to: '#'
  },
  {
    label: 'تعليم الوضوء والصلاة (PDF)',
    to: '#'
  }
]
const submitNewsletter = async (): Promise<void> => {
  try {
    const res = await fetch(`${import.meta.env.VITE_API_URL}/api/newsletter/subscribe`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ email: email.value }),
    })

    const result = await res.json()
    message.value = result.message

    if (result.success) email.value = ''
  } catch {
    message.value = 'An error occurred. Please try again later.'
  }
}
</script>
