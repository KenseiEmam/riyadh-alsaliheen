<template>

      <div v-show="props" class="carousel__item h-[30vh]  w-full  relative">
        <img class="absolute w-full h-full grayscale-60 object-cover" :src="image"/>
        <div class="absolute w-full h-full bg-gradient-to-t from-primary-background to-secondary-background/40 "></div>
        <div dir="rtl" class=" flex flex-col w-full h-full relative items-center justify-center  md:max-w-1/3 text-center space-y-6 mx-auto px-8">
        <h2 class="title-large">{{ title }}</h2>
        </div>
      </div>
    
</template>
<script setup lang="ts">
const props = defineProps(['title', 'image'])


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