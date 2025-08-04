<template>
  <div class="banner-header">
    <router-link to="/home" class="banner-icon">
      <!-- Home-Icon SVG wie gehabt -->
      <svg width="26" height="26" viewBox="0 0 24 24" fill="none">
        <path d="M3 12L12 4l9 8" stroke="#fff" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
        <rect x="6.5" y="13" width="11" height="7" rx="1.5" fill="#fff" stroke="#fff" stroke-width="2"/>
      </svg>
    </router-link>
    <button class="banner-icon" @click="goBack" aria-label="Zurück">
      <!-- Back-Arrow-Icon SVG (z.B. Pfeil nach links) -->
      <svg width="26" height="26" viewBox="0 0 24 24" fill="none">
        <path d="M15 18l-6-6 6-6" stroke="#fff" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
      </svg>
    </button>
    <div class="banner-title">{{ title }}</div>
  </div>
</template>

<script setup lang="ts">
import { useRouter } from 'vue-router'
const router = useRouter()
defineProps<{ title: string }>()

function goBack() {
  router.back()
}
</script>

<style scoped>

.banner-header {
  display: flex;
  align-items: flex-end;
  justify-content: flex-start;
  background: #4D9B85;
  padding-top: calc(env(safe-area-inset-top, 22px) + 1px);
  padding-bottom: 10px;
  padding-left: 18px;
  padding-right: 18px;
  font-family: 'Poppins', sans-serif;
  box-sizing: border-box;
  z-index: 1001;
  position: relative;
}
.banner-icon {
  display: flex;
  align-items: center;
  justify-content: center;
  margin-right: 14px;
  text-decoration: none;
  cursor: pointer;
  background: transparent;
  border: none;
  padding: 0;
}
.banner-title {
  color: #fff;
  font-size: 1.45rem;
  font-weight: 600;
  text-align: center;
  flex: 1;
  letter-spacing: 0.01em;
  margin: 0;
  line-height: 1.08;
  padding-right: 40px;
}
</style>