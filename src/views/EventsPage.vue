<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Deine Termine</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-content>
      <div style="display: flex; gap: 12px; justify-content: center; margin: 18px 0 8px 0;">
        <ion-button expand="block" color="success" router-link="/events/new">
          + Neuer Termin
        </ion-button>
        <ion-button expand="block" color="primary" @click="loadEvents">
          Termine neu laden
        </ion-button>
      </div>
      <ion-list>
        <ion-item
          v-for="event in sortedEvents"
          :key="event.id"
          :router-link="`/events/${event.id}`"
        >
          <ion-label>
            <h2>{{ event.title }}</h2>
            <p>{{ formatDate(event.startDate) }}</p>
            <p v-if="event.location">Ort: {{ event.location }}</p>
          </ion-label>
        </ion-item>
      </ion-list>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { ref, onMounted, computed } from 'vue';
import { IonPage, IonHeader, IonToolbar, IonTitle, IonContent, IonList, IonItem, IonLabel, IonButton } from '@ionic/vue';
import { registerPlugin } from '@capacitor/core';

const CapacitorCalendar = registerPlugin<any>('CapacitorCalendar');

const events = ref<any[]>([]);

const loadEvents = async () => {
  try {
    const result = await CapacitorCalendar.listEventsInRange({
      from: Date.now() - 365 * 24 * 60 * 60 * 1000,
      to: Date.now() + 365 * 24 * 60 * 60 * 1000
    });
    events.value = result.result ?? [];
  } catch (err) {
    console.error('Fehler beim Laden der Termine:', err);
  }
};

const sortedEvents = computed(() =>
  events.value.slice().sort((a, b) => a.startDate - b.startDate)
);

const formatDate = (dateNum: number) => {
  const date = new Date(dateNum);
  return date.toLocaleString();
};

onMounted(() => {
  loadEvents();
});
</script>
