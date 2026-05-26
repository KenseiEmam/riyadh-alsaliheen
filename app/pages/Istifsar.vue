<template>
  <Banner
    title="الاستفسارات"
    image="https://img.magnific.com/free-vector/web-support-help-concept-question-mark-background_1017-59786.jpg"
  />

  <section class="container bg-primary-backgroud py-4 md:py-8 2xl:py-12">
    <div class="mx-auto">
      <h2 class="title-medium mb-4">
        أسئلة ترددت علينا
      </h2>

      <div class="flex flex-col gap-2 md:gap-3 w-full">
        <div
          v-for="(question, index) in questions"
          :key="index"
          class="card cursor-pointer"
          @click="toggleShow(index)"
        >
          <div class="flex items-center justify-between w-full border-b border-teritiary/60">
            <h3 class="md:text-lg">
              {{ question.q }}
            </h3>

            <svg
              :class="{ 'rotate-180': showing === index }"
              class="w-4 h-4 ms-auto transform transition-transform"
              fill="none"
              stroke="currentColor"
              stroke-width="2"
              viewBox="0 0 24 24"
            >
              <path stroke-linecap="round" stroke-linejoin="round" d="M19 9l-7 7-7-7" />
            </svg>
          </div>

          <transition name="dropdown">
            <p
              v-if="showing === index"
              class="py-3 text-body text-base leading-relaxed"
            >
              {{ question.a }}
            </p>
          </transition>
        </div>
      </div>

      <!-- Contact Section -->
      <div class="mt-10 card p-6 space-y-3">
        <h3 class="title-medium">
          أسئلة أكثر تحديداً
        </h3>

        <p class="text-body">
          إذا كان لديك استفسار خاص أو تحتاج إلى معلومات إضافية، يمكنك التواصل معنا مباشرة.
        </p>

        <div class=" flex flex-col md:flex-row gap-3 md:gap-12 md:items-center">
          <a
            href="tel:+201234567890"
            class="text-xl border-b"
          >
            اتصال مباشر
          </a>

          <a
            href="mailto:info@mosque.org"
            class="text-xl border-b"
          >
            إرسال بريد إلكتروني
          </a>
        </div>
      </div>
    </div>
  </section>
</template>

<script lang="ts" setup>
import { ref } from 'vue'

const showing = ref<number | undefined>(undefined)

const questions = [
  {
    q: 'هل تتوفر أماكن للوضوء داخل المسجد؟',
    a: 'نعم، يوجد بالمجسد أماكن مخصصة للوضوء مزودة بأحواض ومرافق نظيفة ومهيأة للمصلين.'
  },
  {
    q: 'هل المصلى الداخلي مكيف؟',
    a: 'نعم، المصلى الداخلي مكيف ومجهز بشكل جيد ويوفر بيئة مريحة للصلاة والعبادة.'
  },
  {
    q: 'هل يوجد مصلى مخصص للنساء؟',
    a: 'لا، لا يوجد قسم مخصص للنساء داخل هذا المسجد حالياً.'
  },
  {
    q: 'كيف يمكن التبرع للمسجد؟',
    a: 'تتوفر صناديق التبرع داخل المسجد، كما يمكن التواصل مع إدارة المسجد للحصول على طرق تبرع أخرى.'
  },
  {
    q: 'متى يفتح المسجد؟',
    a: 'يفتح المسجد أبوابه عادةً قبل كل صلاة ويظل مفتوحاً خلال أوقات الصلاة.'
  },
  {
    q: 'هل تقام صلاة الجمعة في المسجد؟',
    a: 'نعم، تقام صلاة الجمعة داخل المسجد وتمتد أحياناً إلى الساحة الخارجية عند زيادة أعداد المصلين.'
  },
  {
    q: 'هل تقام صلاة العيد؟',
    a: 'نعم، تقام صلاة العيد في الساحة في الخلاء قرب المسجد اتباعاً للسنة النبوية.'
  }
]

function toggleShow(index: number) {
  showing.value = showing.value === index ? undefined : index
}
</script>

<style scoped>
.dropdown-enter-active,
.dropdown-leave-active {
  transition: all 0.3s ease-out;
}

.dropdown-enter-from,
.dropdown-leave-to {
  opacity: 0;
  transform: translateY(-10px);
}
</style>