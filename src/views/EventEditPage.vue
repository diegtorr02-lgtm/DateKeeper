<template>
  <ion-page>
    <ion-content fullscreen scroll-events>
      <banner-header title="Termin als Vorlage verwenden" />

      <div class="monatss-bg">

        <div class="input-block">
          <label class="custom-label">Titel</label>
          <ion-input
            v-model="title"
            placeholder="Titel eingeben"
          />
        </div>

        <div class="input-block">
          <label class="custom-label">Ort</label>
          <ion-input
            v-model="location"
            placeholder="Ort eingeben"
          />
        </div>

        <div class="input-block">
          <label class="custom-label">Startdatum</label>
          <div class="date-tag">{{ formatDate(startDate) }}</div>
          <ion-datetime
            v-model="startDate"
            presentation="date-time"
            :prefer-wheel="true"
            locale="de-DE"
            show-default-buttons
            done-text="OK"
            cancel-text="Abbrechen"
            class="custom-datetime"
            @ionChange="onStartChange"
          />
        </div>

        <div class="input-block">
          <label class="custom-label">Enddatum</label>
          <div class="date-tag">{{ formatDate(endDate) }}</div>
          <ion-datetime
            v-model="endDate"
            presentation="date-time"
            :prefer-wheel="true"
            locale="de-DE"
            show-default-buttons
            done-text="OK"
            cancel-text="Abbrechen"
            class="custom-datetime"
            @ionChange="onEndChange"
          />
        </div>

        <div class="input-block input-row">
          <label class="custom-label">Ganztägig</label>
          <ion-toggle v-model="allDay" color="success" />
        </div>

        <div class="input-block">
          <label class="custom-label">Beschreibung</label>
          <ion-textarea
            v-model="description"
            auto-grow
            :rows="3"
            placeholder="Beschreibung eingeben"
          />
        </div>

        <ion-button expand="full" class="save-btn" @click="saveEvent">Speichern</ion-button>
        <ion-button expand="full" color="medium" @click="router.back()">Abbrechen</ion-button>
      </div>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import { useRouter, useRoute } from 'vue-router'
import { registerPlugin } from '@capacitor/core'
import BannerHeader from '@/BannerHeader.vue'
import {
  IonPage, IonContent, IonInput, IonButton, IonToggle,
  IonDatetime, IonTextarea, alertController
} from '@ionic/vue'

const router = useRouter()
const route = useRoute();
const CapacitorCalendar = registerPlugin<any>('CapacitorCalendar')

const title = ref('')
const location = ref('')
const description = ref('')
const startDate = ref<string>(new Date().toISOString())
const endDate = ref<string>(new Date().toISOString())
const allDay = ref(false)

const eventId = route.params.id as string;

function onStartChange(ev: CustomEvent) {
  if (ev.detail?.value) startDate.value = ev.detail.value
}
function onEndChange(ev: CustomEvent) {
  if (ev.detail?.value) endDate.value = ev.detail.value
}

function formatDate(val: string) {
  const d = new Date(val)
  return d.toLocaleString('de-DE', {
    day: '2-digit', month: 'short', year: 'numeric',
    hour: '2-digit', minute: '2-digit'
  })
}

// Lade die Eventdaten als Vorlage
const loadEvent = async () => {
    if (!eventId) return; // Abbruch, wenn keine ID vorhanden ist

    try {
        const result = await CapacitorCalendar.listEventsInRange({
            from: Date.now() - 2 * 365 * 24 * 60 * 60 * 1000,
            to: Date.now() + 2 * 365 * 24 * 60 * 60 * 1000
        });
        const ev = (result.result ?? []).find((e: any) => String(e.id) === String(eventId));
        if (ev) {
            title.value = ev.title || '';
            location.value = ev.location || '';
            description.value = ev.description || ''; // Füge die Beschreibung hinzu
            startDate.value = new Date(ev.startDate).toISOString();
            endDate.value = new Date(ev.endDate).toISOString();
            allDay.value = !!ev.isAllDay;
        }
    } catch (err) {
        console.error('Fehler beim Laden der Vorlage:', err);
    }
};

async function saveEvent() {
  if (!title.value.trim()) {
    alert('Bitte einen Titel eingeben.')
    return
  }
  try {
    await CapacitorCalendar.createEvent({
      title: title.value,
      location: location.value,
      description: description.value,
      startDate: new Date(startDate.value).getTime(),
      endDate: new Date(endDate.value).getTime(),
      isAllDay: allDay.value
    })
    alert('Termin gespeichert!')
    router.push('/events')
  } catch (err) {
    console.error('Fehler beim Speichern des Termins:', err)
    alert('Fehler beim Speichern. Prüfe Berechtigungen und Kalenderzugriff!')
  }
}

onMounted(() => {
  loadEvent();
});
</script>

<style scoped>
.monatss-bg {
  padding: 20px 12px 40px 12px;
}
.input-block {
  margin-bottom: 18px;
  display: flex;
  flex-direction: column;
  align-items: flex-start;
}
.input-row {
  flex-direction: row;
  align-items: center;
  gap: 14px;
}
.custom-label {
  font-weight: bold;
  font-size: 16px;
  color: #31423c;
  font-family: 'Poppins', sans-serif;
  margin-bottom: 4px;
  letter-spacing: 0.01em;
  display: inline-block;
}
.date-tag {
  background: #d2f5ec;
  color: #207468;
  border-radius: 7px;
  padding: 6px 16px;
  font-size: 15px;
  font-family: 'Poppins', sans-serif;
  margin-bottom: 6px;
  margin-top: 2px;
  box-shadow: 0 1px 5px #4d9b8533;
  border: 1.5px solid #4D9B85;
  min-width: 160px;
}
.custom-datetime {
  --background: #e8f6f1 !important;
  background: #e8f6f1 !important;
  border-radius: 8px;
  width: 100%;
  max-width: 330px;
  border: none;
}
.save-btn {
  --background: #4D9B85 !important;
  margin-top: 12px;
}
.textarea-field {
  margin-top: 5px;
  padding-left: 8px;
}
:deep(ion-content) {
  --background: #e8f6f1 !important;
  background: #e8f6f1 !important;
}
:deep(ion-datetime) {
  --background: #e8f6f1 !important;
  --wheel-fade-background-rgb: 232, 246, 241 !important;
  --wheel-highlight-background: rgba(77, 155, 133, 0.14) !important;
  --wheel-highlight-border-radius: 16px !important;
}
:deep(ion-datetime::part(wheel-item)) {
  color: #207468 !important;
}
</style>