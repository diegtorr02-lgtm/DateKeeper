<template>
  <ion-page>
    <ion-content fullscreen scroll-events>
      <banner-header title="Termin hinzufügen" />

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
          <ion-button expand="block" class="erweiterte-datums-btn" @click="openAdvancedDatepicker">
            Erweiterte Datumeingabe
          </ion-button>
          <label class="custom-label">Beschreibung</label>
          <ion-textarea
            v-model="description"
            auto-grow
            :rows="3"
            placeholder="Beschreibung eingeben"
          />
        </div>

        <ion-button expand="full" class="save-btn" @click="saveEvent">Speichern</ion-button>
        <ion-button expand="full" color="medium" router-link="/events">Abbrechen</ion-button>
      </div>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import { registerPlugin } from '@capacitor/core'
import BannerHeader from '@/BannerHeader.vue'
import {
  IonPage, IonContent, IonInput, IonButton, IonToggle,
  IonDatetime, IonTextarea
} from '@ionic/vue'
import { DatetimePicker } from '@capawesome-team/capacitor-datetime-picker'

const router = useRouter()
const CapacitorCalendar = registerPlugin<any>('CapacitorCalendar')

const title = ref('')
const location = ref('')
const description = ref('')
const startDate = ref<string>(new Date().toISOString())
const endDate = ref<string>(new Date().toISOString())
const allDay = ref(false)

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


async function openAdvancedDatepicker() {
  try {
    const { value } = await DatetimePicker.present({
      cancelButtonText: 'Abbrechen',
      doneButtonText: 'Fertig',
      mode: 'datetime',
      value: startDate.value,
      locale: 'de-DE',
      theme: 'light' 
    });
    
    if (value) {
      startDate.value = value;
      endDate.value = value;
    }
  } catch (e) {
    console.error('Datetime Picker wurde abgebrochen oder hat einen Fehler: ', e);
  }
}


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
  background: #e8f6f1;
  color: #4D9B85;
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
.erweiterte-datums-btn {
  --background: #e8f6f1 !important;
  color: #4D9B85 !important;
  border: 1px solid #4D9B85;
  font-family: 'Poppins', sans-serif;
  font-weight: 500;
  font-size: 1rem;  
  letter-spacing: 0.01em;
  margin-bottom: 12px;
}
</style>

 --background: #4D9B85 !important;
  margin-top: 12px;s