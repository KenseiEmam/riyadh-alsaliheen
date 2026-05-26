<script setup lang="ts">
import FooterVue from '~/components/FooterVue.vue';
import HeaderVue from '~/components/HeaderVue.vue';

</script>

<template>
  <div>
    <HeaderVue />
    <slot />
    <FooterVue />
  </div>
</template>
