<template>
  <div class="banner-header">
    <router-link to="/home" class="banner-icon">
      <svg width="26" height="26" viewBox="0 0 24 24" fill="none">
        <path d="M3 12L12 4l9 8" stroke="#fff" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
        <rect x="6.5" y="13" width="11" height="7" rx="1.5" fill="#fff" stroke="#fff" stroke-width="2"/>
      </svg>
    </router-link>
    <div class="banner-title">
      {{ title }}
    </div>
  </div>
</template>

<script setup lang="ts">
defineProps<{ title: string }>()
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;700&display=swap');

.banner-header {
  display: flex;
  align-items: flex-end;
  justify-content: flex-start;
  background: #4D9B85;
  /* Abstand nach oben wie auf Home, plus Safe-Area für Notch */
  padding-top: calc(env(safe-area-inset-top, 22px) + 8px);
  /* Wenig Platz unten, damit es optisch mittig bleibt */
  padding-bottom: 10px;
  padding-left: 18px;
  padding-right: 18px;
  border-bottom: none;
  font-family: 'Poppins', sans-serif;
  box-sizing: border-box;
  z-index: 1001;
  position: relative;
  min-height: 0;
}
.banner-icon {
  display: flex;
  align-items: center;
  justify-content: center;
  margin-right: 18px;
  text-decoration: none;
  cursor: pointer;
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
}
</style>
