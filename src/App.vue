<script setup lang="ts">
import { computed, ref, watch } from "vue";
import EventList from "./components/EventList.vue";
import { initialEvents } from "./data/events";
import type { CampusEvent } from "./types";

// A reactive copy of the hard-coded events
const events = ref<CampusEvent[]>(initialEvents.map(e => ({ ...e })));

// TODO 1: a ref `myBookings` holding the ids of the events I booked (one entry per seat)
const myBookings = ref<number[]>([])

// TODO 2: a computed `bookingCount`
const bookingCount = computed(() => myBookings.value.length)

// TODO 3: onBook(id): find the event; if it still has a seat,
//         increase its `booked` and add the id to myBookings
function onBook(id: number) {
  // find the event with this id
  const event = events.value.find(e => e.id === id)

  // if no event was found, stop
  if (event === undefined) {
    return
  }

  // if the event is full, stop
  if (event.booked >= event.capacity) {
    return
  }

  // take one seat and remember the booking
  event.booked = event.booked + 1
  myBookings.value.push(id)
}

// TODO 4 (bonus): keep my bookings after a page reload
//   - watch myBookings and save it to localStorage (JSON.stringify)
//   - read it back when the app starts, and add those seats to `booked`
const KEY = 'myBookings'

// read the saved ids; return an empty list if anything is wrong
function loadBookings(): number[] {
  try {
    const text = localStorage.getItem(KEY)
    if (text === null) {
      return []
    }
    const data = JSON.parse(text)
    if (Array.isArray(data)) {
      return data
    }
    return []
  } catch {
    return []
  }
}

// when the app starts: restore the list and the seats
const saved = loadBookings()
myBookings.value = saved
for (const id of saved) {
  const event = events.value.find(e => e.id === id)
  if (event !== undefined && event.booked < event.capacity) {
    event.booked = event.booked + 1
  }
}

// every time myBookings changes: save it
watch(
  myBookings,
  () => {
    localStorage.setItem(KEY, JSON.stringify(myBookings.value))
  },
  { deep: true }
)

</script>

<template>
  <main>
    <header>
      <h1>Campus Events</h1>
      <!-- TODO 5: show the real count ("1 seat" / "N seats") -->
      <p class="mine">
        You have booked {{ bookingCount }} {{ bookingCount === 1 ? "seat" : "seats" }}.
      </p>
    </header>
    <EventList :events="events" @book="onBook" />
  </main>
</template>

<style scoped>
header { margin-bottom: 16px; }
.mine { color: #6b7280; margin: 0; }
</style>
