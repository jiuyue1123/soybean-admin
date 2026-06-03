<script setup lang="ts">
import { computed } from 'vue';

defineOptions({
  name: 'GlobalFooter'
});

const currentYear = computed(() => new Date().getFullYear());
</script>

<template>
  <DarkModeContainer class="h-full flex-center">
    <span>Copyright &copy; {{ currentYear }} Management System</span>
  </DarkModeContainer>
</template>

<style scoped></style>
