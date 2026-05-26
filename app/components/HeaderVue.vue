<template>
  <header class="sticky w-full top-0 z-50 border-b bg-primary-background/80 backdrop-blur-md translate-y-0 opacity-100">
    <div class="container md:px-6 max-w-7xl relative mx-auto flex items-center h-20">
       <!-- Logo -->
      <div class="w-64  flex items-center">
        <div class="flex flex-col gap-1">
          <h3 class="title-small text-sm">مسجد</h3>
          <h1 class="title-medium font-bold tracking-extra-trackinge text-primary">رياض الصالحين</h1>
          <h3 class="title-small text-teritiary text-sm">الاسكندرية</h3>
        </div>
      </div>

          <!-- Desktop Nav -->
      <nav class="hidden md:flex items-center gap-3 ms-auto text-sm font-medium justify-center">
        <RouterLink
          to="/"
          class="hover:text-primary-text text-secondary-text uppercase transition active:text-primary"
        >الصفحة الرئيسية</RouterLink>

        <RouterLink
          to="/Awqat"
          class="hover:text-primary-text text-secondary-text uppercase transition active:text-primary"
        >اوقات الصلاه</RouterLink>

        <RouterLink
          to="/Istifsar"
          class="hover:text-primary-text text-secondary-text uppercase transition active:text-primary"
        >الأسإلة</RouterLink>
        <div
          @mouseenter="showCategories = true"
          @mouseleave="showCategories = false"
          class="relative"
        >
          <RouterLink
            to="/Manshurat"
            class="flex items-center gap-1 hover:text-primary-text text-secondary-text uppercase transition active:text-primary"
          >
            المنشورات
            <svg
              class="w-4 h-4 mt-[1px]"
              fill="none"
              stroke="currentColor"
              stroke-extra-trackingth="2"
              viewBox="0 0 24 24"
            >
              <path stroke-linecap="round" stroke-linejoin="round" d="M19 9l-7 7-7-7" />
            </svg>
          </RouterLink>

          <div
            v-show="showCategories"
            class="absolute top-full w-64 -left-20 rounded-lg mx-auto backdrop-blur-md bg-secondary-background shadow-xl border border-teritiary/40 p-4 z-50"
          >
            <div class="space-y-4 text-sm">
              <div>
                
                <RouterLink
                  v-for="item in manshurat"
                  :key="item.to"
                  :to="item.to"
                  class="block text-secondary-text py-1 px-2 rounded-lg hover:bg-zinc-300 transition"
                >
                  {{ item.label }}
                </RouterLink>
              </div>
            </div>
          </div>
        </div>

        
      </nav>


<!-- Mobile Menu Button -->
<button
  class="md:hidden ms-auto text-secondary-text"
  @click="mobileOpen = !mobileOpen"
>
  <svg
    class="w-6 h-6"
    fill="none"
    stroke="currentColor"
    stroke-width="2"
    viewBox="0 0 24 24"
  >
    <path
      stroke-linecap="round"
      stroke-linejoin="round"
      d="M4 6h16M4 12h16M4 18h16"
    />
  </svg>
</button>

     <!-- Right Controls (desktop only) -->
<!-- <div class="hidden md:flex items-center gap-4 justify-end ms-auto">
  <button class="btn-lg">استفسار</button>
  <button class="btn-lg-outline">تبرع</button>
</div> -->
    </div>
   <!-- Mobile Menu -->
<div
  v-show="mobileOpen"
  class="md:hidden border-t border-teritiary bg-primary-background/95 backdrop-blur-md px-6 py-4 space-y-4"
>
  <nav class="flex flex-col gap-3 text-sm">

    <RouterLink
      to="/"
      class="py-2 text-secondary-text hover:text-primary-text transition"
    >
      الصفحة الرئيسية
    </RouterLink>

    <RouterLink
      to="/Awqat"
      class="py-2 text-secondary-text hover:text-primary-text transition"
    >
      أوقات الصلاة
    </RouterLink>

    <RouterLink
      to="/Istifsar"
      class="py-2 text-secondary-text hover:text-primary-text transition"
    >
      الأسئلة
    </RouterLink>

    <RouterLink
      to="/Manshurat"
      class="py-2 text-secondary-text hover:text-primary-text transition"
    >
      المنشورات
    </RouterLink>
<!-- 
    <div class="pt-4 border-t border-teritiary space-y-2">
      <button class="btn-lg w-full">
        استفسار
      </button>

      <button class="btn-lg-outline w-full">
        تبرع
      </button>
    </div> -->
  </nav>
</div>
</header>
</template>

<script setup lang="ts">
import { ref, watch } from 'vue'
import { useRoute } from 'vue-router'

const showCategories = ref(false)
const mobileOpen = ref(false)
const route = useRoute()

watch(() => route.fullPath, () => {
  mobileOpen.value = false
})
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


</script>
