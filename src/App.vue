<template>
  <ion-app>
    <router-view />
  </ion-app>
</template>

<script setup lang="ts">
import { IonApp } from '@ionic/vue';
import { onMounted } from 'vue';
import { registerPlugin } from '@capacitor/core';

const CapacitorCalendar = registerPlugin<any>('CapacitorCalendar');

onMounted(async () => {
  try {
    // Prüfe zuerst den Status der Berechtigungen
    const status = await CapacitorCalendar.checkAllPermissions();
    // Wenn noch nicht erlaubt, dann anfordern
    if (
      status.result?.readCalendar !== 'granted' ||
      status.result?.writeCalendar !== 'granted'
    ) {
      const perm = await CapacitorCalendar.requestAllPermissions();
      if (
        perm.result?.readCalendar !== 'granted' ||
        perm.result?.writeCalendar !== 'granted'
      ) {
        // Optional: Freundlicher Hinweis (Toast oder alert)
        alert("Bitte Kalender-Berechtigung aktivieren, damit die App funktioniert.");
      }
    }
  } catch (error) {
    console.error('Fehler bei der Kalender-Berechtigung:', error);
  }
});
</script>
