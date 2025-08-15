<template>
  <ion-page>
    <banner-header title="Termin-Details" />
    <ion-content fullscreen class="events-bg">
      <div v-if="event">
        <div class="event-detail-card">
          <h2>{{ event.title }}</h2>
          <p><strong>Ort:</strong> {{ event.location || '–' }}</p>
          <p><strong>Start:</strong> {{ formatDate(event.startDate) }}</p>
          <p><strong>Ende:</strong> {{ formatDate(event.endDate) }}</p>
          <p><strong>Ganztägig:</strong> {{ event.isAllDay ? 'Ja' : 'Nein' }}</p>
          <p v-if="event.description">
            <strong>Beschreibung:</strong><br />
            {{ event.description }}
          </p>
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
          <ion-button
            expand="block"
            color="primary"
            :router-link="`/events/edit/${event.id}`"
          >
            Termin als Vorlage verwenden
          </ion-button>
          <ion-button expand="block" fill="outline" class="delete-btn" @click="confirmDelete">
            Termin löschen
          </ion-button>
        </div>
      </div>
      <div v-else style="text-align:center; margin-top:80px;color: #4D9B85;">
        <ion-spinner name="crescent" />
        <p>Termin wird geladen…</p>
      </div>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">




import BannerHeader from '@/BannerHeader.vue'
import {
  IonPage,
  IonContent,
  IonButton,
  IonSpinner,
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

<style scoped>
:deep(ion-content) {
  --background: #e8f6f1 !important;
  background: #e8f6f1 !important;
}
.events-bg {
  background: #e8f6f1 !important;
  min-height: 100vh;
  width: 100vw;
  padding: 0;
  box-sizing: border-box;
}
.event-detail-card {
  margin: 30px auto 0 auto;
  max-width: 420px;
  background: #fff;
  border-radius: 20px;
  box-shadow: 0 4px 16px #0001;
  padding: 28px 22px 22px 22px;
  font-family: 'Poppins', sans-serif;
  color: #31423c;
  font-size: 1.08rem;
  line-height: 1.6;
}
.event-detail-card h2 {
  font-size: 1.5rem;
  font-weight: 700;
  color: #365d58;
  margin-bottom: 16px;
  margin-top: 0;
}
.delete-btn {
  --background: #4D9B85
  --color: #e74c3c;
  --border-color: #e74c3c;
  font-weight: 600;
  margin-top: 10px;
  margin-bottom: 6px;
}
</style>
