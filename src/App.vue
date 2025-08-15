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
    const status = await CapacitorCalendar.checkAllPermissions();
    if (
      status.result?.readCalendar !== 'granted' ||
      status.result?.writeCalendar !== 'granted'
    ) {
      const perm = await CapacitorCalendar.requestAllPermissions();
      if (
        perm.result?.readCalendar !== 'granted' ||
        perm.result?.writeCalendar !== 'granted'
      ) {
        alert("Bitte Kalender-Berechtigung aktivieren, damit die App funktioniert.");
      }
    }
  } catch (error) {
    console.error('Fehler bei der Kalender-Berechtigung:', error);
  }
});
</script>
