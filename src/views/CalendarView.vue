<template>
  <ion-page>
    <ion-content fullscreen>
      <banner-header title="Kalenderübersicht" />
      <div class="monat-bg">
        <div class="monat-inner">
          <ion-button
            expand="block"
            class="gruen-btn"
            @click="loadEvents"
            style="margin-bottom: 12px;"
          >
            Termine neu laden
          </ion-button>
          <vue-cal
            :events="calendarEvents"
            default-view="month"
            class="vuecal-embed"
            @event-click="onEventClick"
            locale="de"
            hide-view-selector
          />
        </div>
      </div>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import BannerHeader from '@/BannerHeader.vue'
// @ts-ignore
import VueCal from 'vue-cal';
import 'vue-cal/dist/vuecal.css';
import {
  IonPage,
  IonContent,
  IonButton
} from '@ionic/vue';
import { ref, onMounted } from 'vue';
import { registerPlugin } from '@capacitor/core';
import { useRouter } from 'vue-router';

const CapacitorCalendar = registerPlugin<any>('CapacitorCalendar');
const router = useRouter();

const calendarEvents = ref<any[]>([]);

const loadEvents = async () => {
  try {
    const result = await CapacitorCalendar.listEventsInRange({
      from: Date.now() - 365 * 24 * 60 * 60 * 1000,
      to: Date.now() + 365 * 24 * 60 * 60 * 1000
    });
    calendarEvents.value = (result.result ?? []).map((event: any) => ({
      start: new Date(event.startDate),
      end: new Date(event.endDate),
      title: event.title,
      content: event.location,
      id: event.id,
      background: "#1bb99a",
    }));
  } catch (err) {
    console.error('Fehler beim Laden der Termine:', err);
  }
};

const onEventClick = (event: any) => {
  const e = event?.event ?? event;
  if (e.id) {
    router.push(`/events/${e.id}`);
  } else {
    alert('Kein Event-ID gefunden:\n' + JSON.stringify(e, null, 2));
  }
};

onMounted(() => {
  loadEvents();
});
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;700&display=swap');

.monat-bg {
  background: #e8f6f1;
  min-height: 100vh;
  width: 100vw;
  display: flex;
  justify-content: center;
}
.monat-inner {
  width: 100%;
  max-width: 600px;
  margin: 0 auto;
  padding-top: 16px;
}

.vuecal-embed {
  background: transparent !important;
  box-shadow: 0 4px 16px #0001;
  border-radius: 16px;
  margin-top: 4px;
  font-family: 'Poppins', sans-serif;
  min-height: 480px;
}

:deep(.vuecal) {
  background: transparent !important;
  border-radius: 16px;
}
:deep(.vuecal__view), 
:deep(.vuecal__background) {
  background: transparent !important;
}

.gruen-btn {
  --background: #4D9B85 !important;
  --background-hover: #365D58 !important;
  --background-activated: #365D58 !important;
  --background-focused: #365D58 !important;
  background: #4D9B85 !important;
  color: #fff !important;
  border-radius: 7px;
  font-family: 'Poppins', sans-serif;
  font-weight: 500;
  font-size: 1.1rem;
  letter-spacing: 0.01em;
}
.gruen-btn:hover,
.gruen-btn:active,
.gruen-btn:focus {
  background: #365D58 !important;
  --background: #365D58 !important;
}
</style>
