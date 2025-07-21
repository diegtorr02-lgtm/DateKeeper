<template>
  <ion-page>
    <ion-content class="home-gradient">
      <div class="home-wrapper">
        <img src="/assets/icon.png" alt="App-Icon" class="home-logo" />

        <h1 class="headline">
          Willkommen bei<br />
          <span class="brand">DateKeeper</span>
        </h1>
        <div class="subtitle">
          Alle Termine immer im Griff!
        </div>

        <div class="button-stack">
          <ion-button size="large" expand="block" color="primary" router-link="/events">
            <span class="button-text">📅 Termine ansehen</span>
          </ion-button>
          <ion-button size="large" expand="block" color="success" router-link="/events/new" class="new-btn">
            <span class="button-text">＋ Neuer Termin</span>
          </ion-button>
          <ion-button expand="block" color="tertiary" router-link="/calendar">🗓 Monatsübersicht
          </ion-button>
        </div>

        <div class="welcome-card">
          <p>
            <strong>Probiere alle Funktionen aus:</strong><br />
            – Eigene Termine speichern<br />
            – Übersicht, Detailansicht, Karte<br />
            – Modernes Design und einfache Bedienung
          </p>
        </div>
      </div>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { IonPage, IonContent, IonButton } from '@ionic/vue';
</script>

<style scoped>
.home-gradient {
  --background: linear-gradient(135deg, #d3f8e2 0%, #8ee3ef 60%, #5ea3a3 100%);
  min-height: 100vh;
}

.home-wrapper {
  max-width: 410px;
  margin: 0 auto;
  padding: 44px 18px 0 18px;
  display: flex;
  flex-direction: column;
  align-items: center;
  min-height: 100vh;
}

.home-logo {
  width: 95px;
  height: 95px;
  margin-bottom: 22px;
  border-radius: 20px;
  box-shadow: 0 6px 30px 0 rgba(40, 200, 180, 0.12);
  display: block;
  background: #e3f6f5;
  object-fit: contain;
  border: 2.5px solid #7fe0ca25;
}

.headline {
  font-size: 2.1rem;
  font-weight: 800;
  text-align: center;
  margin-bottom: 10px;
  letter-spacing: -1px;
  color: #227c9d;
}
.brand {
  color: #14b89e;
  font-size: 2.22rem;
  font-weight: 900;
  letter-spacing: -1.2px;
  font-family: inherit;
}

.subtitle {
  font-size: 1.18rem;
  font-weight: 500;
  color: #14b89e;
  opacity: 0.92;
  margin-bottom: 32px;
  text-align: center;
}

.button-stack {
  width: 100%;
  display: flex;
  flex-direction: column;
  gap: 15px;
  margin-bottom: 28px;
}

.button-text {
  font-size: 1.14rem;
  font-weight: 700;
  letter-spacing: 0.01em;
}

.welcome-card {
  width: 100%;
  background: #fff;
  border-radius: 18px;
  box-shadow: 0 2px 20px 0 rgba(30, 160, 120, 0.10);
  padding: 18px 18px 14px 18px;
  text-align: center;
  color: #1e9e86;
  font-size: 1.03rem;
  margin-top: 12px;
}

@media (max-width: 440px) {
  .home-wrapper {
    padding: 28px 4vw 0 4vw;
  }
}
</style>
