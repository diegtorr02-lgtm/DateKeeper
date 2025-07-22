<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-buttons slot="start">
          <ion-back-button :default-href="`/events/${eventId}`" />
        </ion-buttons>
        <ion-title>Termin bearbeiten</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-content class="ion-padding">
      <form @submit.prevent="updateEvent">
        <ion-item>
          <ion-label position="stacked">Titel</ion-label>
          <ion-input v-model="title" required />
        </ion-item>
        <ion-item>
          <ion-label position="stacked">Ort</ion-label>
          <ion-input v-model="location" />
        </ion-item>
        <ion-item>
          <ion-label position="stacked">Startdatum</ion-label>
          <ion-datetime-button datetime="startPicker" />
        </ion-item>
        <ion-datetime
          id="startPicker"
          v-model="startDate"
          presentation="date-time"
          :prefer-wheel="true"
        />
        <ion-item>
          <ion-label position="stacked">Enddatum</ion-label>
          <ion-datetime-button datetime="endPicker" />
        </ion-item>
        <ion-datetime
          id="endPicker"
          v-model="endDate"
          presentation="date-time"
          :prefer-wheel="true"
        />
        <ion-item>
          <ion-label>Ganztägig</ion-label>
          <ion-toggle v-model="allDay" />
        </ion-item>
        <ion-button expand="full" color="primary" type="submit">Speichern</ion-button>
        <ion-button expand="full" color="medium" :router-link="`/events/${eventId}`">Abbrechen</ion-button>
      </form>
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
  IonItem,
  IonLabel,
  IonInput,
  IonButton,
  IonToggle,
  IonDatetime,
  IonDatetimeButton,
  IonBackButton,
  IonButtons,
  toastController,
} from '@ionic/vue';
import { ref, onMounted } from 'vue';
import { useRoute, useRouter } from 'vue-router';
import { registerPlugin } from '@capacitor/core';

const CapacitorCalendar = registerPlugin<any>('CapacitorCalendar');
const route = useRoute();
const router = useRouter();

const eventId = route.params.id as string;
const title = ref('');
const location = ref('');
const startDate = ref('');
const endDate = ref('');
const allDay = ref(false);
const originalEvent = ref<any>(null);

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
    const ev = (result.result ?? []).find((e: any) => String(e.id) === String(eventId));
    if (ev) {
      originalEvent.value = ev;
      title.value = ev.title || '';
      location.value = ev.location || '';
      startDate.value = new Date(ev.startDate).toISOString();
      endDate.value = new Date(ev.endDate).toISOString();
      allDay.value = !!ev.isAllDay;
    }
  } catch (err) {
    console.error('Fehler beim Laden des Termins:', err);
  }
};

const updateEvent = async () => {
  if (!title.value) {
    await showToast('Bitte einen Titel eingeben.');
    return;
  }
  try {
    // Workaround: Lösche alten Termin, lege neuen an
    await CapacitorCalendar.deleteEvent({ id: originalEvent.value.id });
    await CapacitorCalendar.createEvent({
      title: title.value,
      location: location.value,
      startDate: new Date(startDate.value).getTime(),
      endDate: new Date(endDate.value).getTime(),
      isAllDay: allDay.value
    });
    await showToast('Termin geändert!');
    router.push(`/events/${eventId}`); // zurück zur Detailansicht
  } catch (error) {
    console.error('Fehler beim Aktualisieren des Termins:', error);
    await showToast('Fehler beim Aktualisieren!');
  }
};

onMounted(() => {
  loadEvent();
});
</script>
