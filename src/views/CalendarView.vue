<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Monatsübersicht</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-content fullscreen>
      <div style="padding: 8px; height: 100vh; box-sizing: border-box;">
        <ion-button expand="block" color="primary" @click="loadEvents" style="margin-bottom: 12px;">
          Termine neu laden
        </ion-button>
        <vue-cal
          :events="calendarEvents"
          default-view="month"
          style="height: calc(100vh - 112px);"
          @event-click="onEventClick"
          locale="de"
          hide-view-selector
        />
      </div>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
// @ts-ignore
import VueCal from 'vue-cal';
import 'vue-cal/dist/vuecal.css';
import { IonPage, IonHeader, IonToolbar, IonTitle, IonContent, IonButton } from '@ionic/vue';
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
  // Je nach vue-cal-Version liefert es entweder das Event direkt oder ein Objekt mit { event }
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
