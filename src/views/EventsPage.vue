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
        <ion-button expand="block" fill="clear" @click="toggleFilter">
          <ion-icon :icon="filterIcon" slot="icon-only"></ion-icon>
        </ion-button>
      </div>

      <!-- Suchfeld (sichtbar bei showFilter) -->
      <div v-if="showFilter" style="padding: 0 16px 12px 16px;">
        <ion-input
          v-model="searchQuery"
          placeholder="Nach Titel filtern..."
          clear-input
        ></ion-input>
      </div>

      <ion-list>
        <ion-item
          v-for="event in filteredEvents"
          :key="event.id"
          :router-link="`/events/${event.id}`"
          :class="{
            'high-priority': String(event.priority || '').toLowerCase() === 'hoch'
          }"
        >
          <ion-label>
            <h2>{{ event.title }}</h2>
            <p>{{ formatDate(event.startDate) }}</p>
            <p v-if="event.location">Ort: {{ event.location }}</p>
            <p v-if="event.priority">Priorität: {{ event.priority }}</p>
          </ion-label>
        </ion-item>
      </ion-list>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { ref, onMounted, computed } from 'vue';
import {
  IonPage,
  IonHeader,
  IonToolbar,
  IonTitle,
  IonContent,
  IonList,
  IonItem,
  IonLabel,
  IonButton,
  IonInput,
  IonIcon
} from '@ionic/vue';
import { searchOutline } from 'ionicons/icons';
import { registerPlugin } from '@capacitor/core';

const CapacitorCalendar = registerPlugin<any>('CapacitorCalendar');

const events = ref<any[]>([]);
const showFilter = ref(false);
const searchQuery = ref('');

// Sortierlogik: Hoch → Mittel → Niedrig → Rest → nach Datum
const priorityOrder = new Map([
  ['hoch', 0],
  ['mittel', 1],
  ['niedrig', 2],
]);

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
  events.value.slice().sort((a, b) => {
    const pa = priorityOrder.get(String(a.priority || '').toLowerCase()) ?? 3;
    const pb = priorityOrder.get(String(b.priority || '').toLowerCase()) ?? 3;
    if (pa !== pb) return pa - pb;
    return a.startDate - b.startDate;
  })
);

const filteredEvents = computed(() =>
  sortedEvents.value.filter((event) =>
    event.title?.toLowerCase().includes(searchQuery.value.toLowerCase())
  )
);

const formatDate = (dateNum: number) => {
  const date = new Date(dateNum);
  return date.toLocaleString();
};

const toggleFilter = () => {
  showFilter.value = !showFilter.value;
};

const filterIcon = searchOutline;

onMounted(() => {
  loadEvents();
});
</script>

<style scoped>
.high-priority {
  border-left: 4px solid #e74c3c;
  background-color: #fff6f6;
}
</style>
