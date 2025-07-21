<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Neuer Termin</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-content class="ion-padding">
      <ion-item>
        <ion-label position="stacked">Titel</ion-label>
        <ion-input v-model="title" placeholder="Titel eingeben" />
      </ion-item>

      <ion-item>
        <ion-label position="stacked">Ort</ion-label>
        <ion-input v-model="location" placeholder="Ort eingeben" />
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

      <ion-button expand="full" color="primary" @click="saveEvent">Speichern</ion-button>
      <ion-button expand="full" color="medium" router-link="/events">Abbrechen</ion-button>
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
  IonDatetimeButton
} from '@ionic/vue';
import { ref } from 'vue';
import { useRouter } from 'vue-router';
import { registerPlugin } from '@capacitor/core';

// Korrekte Registrierung nach ebarooni-Doku!
const CapacitorCalendar = registerPlugin<any>('CapacitorCalendar');
const router = useRouter();

const title = ref('');
const location = ref('');
const startDate = ref(new Date().toISOString());
const endDate = ref(new Date().toISOString());
const allDay = ref(false);

const saveEvent = async () => {
  if (!title.value) {
    alert('Bitte einen Titel eingeben.');
    return;
  }

  try {
    await CapacitorCalendar.createEvent({
      title: title.value,
      location: location.value,
      startDate: new Date(startDate.value).getTime(), // Plugin erwartet Zahl!
      endDate: new Date(endDate.value).getTime(),
      isAllDay: allDay.value
    });

    // Optional: Toast, dass gespeichert wurde!
    alert('Termin gespeichert!');
    router.push('/events');
  } catch (error) {
    console.error('Fehler beim Speichern des Termins:', error);
    alert('Fehler beim Speichern. Prüfe Berechtigungen und Kalenderzugriff!');
  }
};
</script>
