<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-buttons slot="start">
          <ion-back-button defaultHref="/events" />
        </ion-buttons>
        <ion-title>Termin-Details</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content class="ion-padding">
      <div v-if="event">
        <h2>{{ event.title }}</h2>
        <p><strong>Ort:</strong> {{ event.location || '–' }}</p>
        <p><strong>Start:</strong> {{ formatDate(event.startDate) }}</p>
        <p><strong>Ende:</strong> {{ formatDate(event.endDate) }}</p>
        <p><strong>Ganztägig:</strong> {{ event.isAllDay ? 'Ja' : 'Nein' }}</p>
        <p v-if="event.priority"
           :style="event.priority === 'hoch' ? 'color:#e74c3c;font-weight:bold;' : ''">
          <strong>Priorität:</strong> {{ event.priority }}
        </p>

        <ion-button expand="block" color="primary" :href="mapsUrl" target="_blank" v-if="event.location">
          Ort auf Karte anzeigen
        </ion-button>
        <ion-button expand="block" @click="copyLocation" v-if="event.location">
          Ort kopieren
        </ion-button>
        <ion-button expand="block" color="danger" @click="confirmDelete">
          Termin löschen
        </ion-button>
        <ion-button expand="block" color="warning" :router-link="`/events/edit/${event.id}`">Termin bearbeiten
        </ion-button>

      </div>
      <div v-else>
        <p>Termin wird geladen…</p>
      </div>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import {
  IonPage,
  IonHeader,
  IonToolbar,
  IonTitle,
  IonContent,
  IonButtons,
  IonBackButton,
  IonButton,
  alertController,
  toastController,
} from '@ionic/vue';
import { useRoute, useRouter } from 'vue-router';
import { ref, onMounted, computed } from 'vue';
import { Clipboard } from '@capacitor/clipboard';
import { registerPlugin } from '@capacitor/core';

const CapacitorCalendar = registerPlugin<any>('CapacitorCalendar');

const route = useRoute();
const router = useRouter();
const event = ref<any>(null);
const eventId = route.params.id as string;

// Toast-Helfer
const showToast = async (message: string) => {
  const toast = await toastController.create({
    message,
    duration: 1500,
    color: 'success'
  });
  await toast.present();
};

const loadEvent = async () => {
  try {
    const result = await CapacitorCalendar.listEventsInRange({
      from: Date.now() - 2 * 365 * 24 * 60 * 60 * 1000,
      to: Date.now() + 2 * 365 * 24 * 60 * 60 * 1000
    });
    event.value = (result.result ?? []).find((e: any) => String(e.id) === String(eventId));
  } catch (err) {
    console.error('Fehler beim Laden des Termins:', err);
  }
};

const formatDate = (dateNum: number) => {
  if (!dateNum) return '-';
  const date = new Date(dateNum);
  return date.toLocaleString();
};

const mapsUrl = computed(() =>
  event.value?.location ? `https://maps.google.com/?q=${encodeURIComponent(event.value.location)}` : ''
);

const copyLocation = async () => {
  await Clipboard.write({ string: event.value.location });
  await showToast('Ort wurde in die Zwischenablage kopiert.');
};

const confirmDelete = async () => {
  const alert = await alertController.create({
    header: 'Termin löschen?',
    message: 'Bist du sicher, dass du diesen Termin löschen willst?',
    buttons: [
      {
        text: 'Abbrechen',
        role: 'cancel',
      },
      {
        text: 'Löschen',
        role: 'destructive',
        handler: async () => {
          await CapacitorCalendar.deleteEvent({ id: event.value.id });
          await showToast('Termin gelöscht!');
          router.push('/events');
        },
      },
    ],
  });

  await alert.present();
};

onMounted(() => {
  loadEvent();
});
</script>
