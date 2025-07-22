<template>
  <div class="homepage-outer">
    <div class="homepage-container">
      <div class="welcome-title">Willkommen bei</div>
      <img src="@/assets/Icon.png" class="calendar-img" alt="Kalender Icon" />
      <div class="brand-title">Date<span class="keeper">Keeper</span></div>
      <div class="subtitle">Alle Termine auf einen Blick.</div>

      <div class="button-list">
        <router-link to="/events/new" class="home-btn">
          <span class="btn-icon">
            <!-- Plus im Kreis -->
            <svg width="28" height="28" viewBox="0 0 32 32" fill="none">
              <circle cx="16" cy="16" r="14" stroke="white" stroke-width="2" fill="none"/>
              <path d="M16 11v10M11 16h10" stroke="white" stroke-width="2" stroke-linecap="round"/>
            </svg>
          </span>
          <span class="btn-text">Neuen Termin<br />hinzufügen</span>
        </router-link>
        <router-link to="/events" class="home-btn">
          <span class="btn-icon">
            <!-- Liste -->
            <svg width="28" height="28" viewBox="0 0 32 32" fill="none">
              <rect x="9" y="10" width="14" height="2" rx="1" fill="white"/>
              <rect x="9" y="15" width="14" height="2" rx="1" fill="white"/>
              <rect x="9" y="20" width="14" height="2" rx="1" fill="white"/>
              <rect x="6" y="10" width="2" height="2" rx="1" fill="white"/>
              <rect x="6" y="15" width="2" height="2" rx="1" fill="white"/>
              <rect x="6" y="20" width="2" height="2" rx="1" fill="white"/>
            </svg>
          </span>
          <span class="btn-text">Termine ansehen</span>
        </router-link>
        <router-link to="/calendar" class="home-btn">
          <span class="btn-icon">
            <!-- Kalender -->
            <svg width="28" height="28" viewBox="0 0 32 32" fill="none">
              <rect x="6" y="10" width="20" height="16" rx="3" fill="white"/>
              <rect x="6" y="15" width="20" height="2" fill="#31a297"/>
              <rect x="10" y="18" width="2" height="2" fill="#31a297"/>
              <rect x="14" y="18" width="2" height="2" fill="#31a297"/>
              <rect x="18" y="18" width="2" height="2" fill="#31a297"/>
              <rect x="22" y="18" width="2" height="2" fill="#31a297"/>
              <rect x="10" y="22" width="2" height="2" fill="#31a297"/>
              <rect x="14" y="22" width="2" height="2" fill="#31a297"/>
              <rect x="18" y="22" width="2" height="2" fill="#31a297"/>
              <rect x="22" y="22" width="2" height="2" fill="#31a297"/>
              <!-- Kalender-Ringe oben -->
              <rect x="10" y="8" width="2" height="4" rx="1" fill="white"/>
              <rect x="20" y="8" width="2" height="4" rx="1" fill="white"/>
            </svg>
          </span>
          <span class="btn-text">Monatsübersicht</span>
        </router-link>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
// Kein Script nötig!
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;700&display=swap');

.homepage-outer {
  min-height: 100vh;
  display: flex;
  align-items: flex-start;
  justify-content: center;
  background: #e8f6f1;
}

.homepage-container {
  width: 100%;
  max-width: 400px;
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-top: 48px; /* Verschiebt alles nach unten */
}

.welcome-title {
  font-size: 2rem;
  font-weight: 700;
  color: #365D58;
  margin-bottom: 12px;
}

.calendar-img {
  width: 90px;
  margin: 0 auto 10px auto;
  display: block;
}

.brand-title {
  font-size: 2rem;
  font-weight: 700;
  margin-bottom: 0;
  margin-top: 5px;
  color: #707070;
  text-align: center;
}

.keeper {
  color: #4D9B85;
}

.subtitle {
  color: #707070;
  font-size: 1.1rem;
  margin-bottom: 42px;
  margin-top: 2px;
  text-align: center;
}

.button-list {
  display: flex;
  flex-direction: column;
  gap: 20px;
  width: 100%;
  align-items: center;
}

.home-btn {
  display: flex;
  align-items: center;
  justify-content: flex-start;
  gap: 18px;
  width: 100%;
  min-width: 240px;
  max-width: 320px;
  height: 58px;
  background: #4D9B85;
  color: white;
  border-radius: 7px;
  font-size: 1.14rem;
  font-family: 'Poppins', sans-serif;
  font-weight: 500;
  text-decoration: none;
  transition: background 0.2s;
  box-shadow: 0 2px 12px #365d5840;
  border: none;
  padding: 0 22px;
  margin: 0 auto;
}

.home-btn:hover {
  background: #365D58;
}

.btn-icon {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 32px;
  width: 36px;
  min-width: 36px;
}

.btn-text {
  display: block;
  text-align: left;
  font-size: 1.11rem;
  font-family: 'Poppins', sans-serif;
  font-weight: 500;
  line-height: 1.22;
}
</style>
