<template>
  <ion-page>
    <ion-content fullscreen>
      <banner-header title="Deine Termine" />
        <div class="monats-bg">
          <ion-refresher slot="fixed" @ionRefresh="handleRefresh">
            <ion-refresher-content
              pulling-text="Liste aktualisieren…"
              refreshing-spinner="circles"
              refreshing-text="Aktualisiere…"
            />
          </ion-refresher>
          <div v-if="loading" class="events-loader">
            <ion-spinner name="crescent" />
            <p>Termine werden geladen…</p>
          </div>
          <template v-else>
            <div class="top-actions-row">
              <ion-button
                class="gruen-btn"
                router-link="/events/new"
              >
                + Neuer Termin
              </ion-button>
          
              <ion-button
                class="gruen-btn kalender-btn"
                router-link="/calendar"
              >
                Kalenderübersicht
              </ion-button>
              <button class="icon-btn" @click="toggleFilter">
                <ion-icon :icon="filterIcon"></ion-icon>
              </button>
            </div>
            <div class="pull-text">
              Zum Neuladen der Termine nach unten ziehen.
            </div>
            <div v-if="showFilter" class="filter-row">
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
                button
                @click="openDetail(event.id)"
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
          </template>
      </div>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { ref, onMounted, computed } from 'vue'
import { onIonViewWillEnter } from '@ionic/vue'
import { useRouter } from 'vue-router'
import { registerPlugin } from '@capacitor/core'
import { searchOutline } from 'ionicons/icons'
import BannerHeader from '@/BannerHeader.vue'
import {
  IonPage,
  IonContent,
  IonRefresher,
  IonRefresherContent,
  IonList,
  IonItem,
  IonLabel,
  IonSpinner,
  IonButton,
  IonIcon,
  IonInput,
} from '@ionic/vue'

const router = useRouter()
const CapacitorCalendar = registerPlugin<any>('CapacitorCalendar')

const events      = ref<any[]>([])
const loading     = ref(true)
const showFilter  = ref(false)
const searchQuery = ref('')


async function loadEvents() {
  loading.value = true
  try {
    const res = await CapacitorCalendar.listEventsInRange({
      from: Date.now() - 365 * 24 * 3600 * 1000,
      to:   Date.now() + 365 * 24 * 3600 * 1000,
    })
    events.value = res.result ?? []
  } catch (e) {
    console.error('Fehler beim Laden der Termine:', e)
  }
  loading.value = false
}


onMounted(() => {
  loadEvents()
})
onIonViewWillEnter(() => {
  loadEvents()
})

const handleRefresh = async (e: any) => {
  await loadEvents()
  e.target.complete()
}

const priorityOrder = new Map([['hoch',0],['mittel',1],['niedrig',2]])
const sortedEvents = computed(() =>
  events.value
    .slice()
    .sort((a,b) => {
      const pa = priorityOrder.get((a.priority||'').toLowerCase()) ?? 3
      const pb = priorityOrder.get((b.priority||'').toLowerCase()) ?? 3
      return pa !== pb ? pa-pb : a.startDate-b.startDate
    })
)
const filteredEvents = computed(() =>
  sortedEvents.value.filter(e =>
    e.title?.toLowerCase().includes(searchQuery.value.toLowerCase())
  )
)

function openDetail(id: string|number) {
  router.push(`/events/${id}`)
}
function toggleFilter() {
  showFilter.value = !showFilter.value
}
const filterIcon = searchOutline
const formatDate = (n: number) => new Date(n).toLocaleString()
</script>

<style scoped>
.monats-bg,
:deep(.ion-page),
:deep(ion-content),
:deep(ion-content .inner-scroll) {
  background: #e8f6f1;
  padding-top: 2px;
  padding-bottom: 1000px;
}

.events-banner-wrapper {
  position: relative;
  z-index: 1001;
}

.top-actions-row {
  display: flex;
  align-items: center;
  justify-content: flex-start;
  margin: 22px 0 0 0;
  gap: 8px;
  padding-left: 24px;
  padding-right: 10px;
  flex-wrap: nowrap;
}

.gruen-btn {
  --background: #4D9B85 !important;
  --background-hover: #365D58 !important;
  --background-activated: #365D58 !important;
  --background-focused: #365D58 !important;
  background: #4D9B85 !important;
  color: #fff !important;
  border-radius: 7px;
  font-family: 'Poppins', sans-serif;
  font-weight: 600;
  font-size: 1.05rem;
  letter-spacing: 0.01em;
  min-width: 110px;
  max-width: 1px;
  height: 44px;
  display: flex;
  align-items: center;
  justify-content: center;
  margin: 0;
  box-shadow: 0 4px 10px #2a5a4514;
}

.kalender-btn {
  min-width: 150px;
  max-width: 220px;;
}

.icon-btn {
  background: #4D9B85;
  border: none;
  border-radius: 50%;
  width: 44px;
  height: 44px;
  margin-left: 2px;
  flex-shrink: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 4px 10px #2a5a4514;
  cursor: pointer;
  transition: background 0.15s;
}
.icon-btn ion-icon {
  color: #fff;
  font-size: 1.8rem;
}

.pull-text {
  margin-top: 16px;
  margin-bottom: 2px;
  margin-left: 26px;
  color: #6c857c;
  font-size: 1.01rem;
  font-family: 'Poppins', sans-serif;
}

.filter-row {
  padding: 0 16px 12px 16px;
}

.events-loader {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 180px;
  color: #4D9B85;
}

:deep(ion-list) {
  background: transparent !important;
}
:deep(ion-item) {
  --background: transparent !important;
  background: transparent !important;
}
:deep(ion-label) {
  background: transparent !important;
}

.high-priority {
  border-left: 4px solid #e74c3c;
  background-color: #fff6f6;
}
</style>
