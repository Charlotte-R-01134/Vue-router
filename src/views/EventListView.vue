<script setup>
import { ref, onMounted, computed } from 'vue'
import EventService from '@/services/EventService.js'
import EventCard from '@/components/EventCard.vue'
import { watchEffect } from 'vue'

const events = ref(null)
const props = defineProps({
  ['page']: {
    type: Number,
    default: 1,
  },
})
const totalEvents = ref(0)
const hasNextPage = computed(() => {
  var totalPages = Math.ceil(totalEvents.value / 2)
  return props.page < totalPages
})

onMounted(() => {
  watchEffect(() => {
    events.value = ref(null)
    EventService.getEvents(2, props.page)
      .then((response) => {
        events.value = response.data
        totalEvents.value = response.headers['x-total-count']
      })
      .catch((error) => {
        console.log(error)
      })
  })
})
</script>

<template>
  <h1>Events For Good</h1>
  <div class="events">
    <EventCard v-for="event in events" :key="event.id" :event="event" />
    <div class="pagination"> 
      <router-link
        id="prev-page"
        :to="{ name: 'EventList', query: { page: props.page - 1 } }"
        rel="prev"
        v-if="page != 1"
        >&#60; Previous</router-link>
      <router-link
        id="next-page"
        :to="{ name: 'EventList', query: { page: props.page + 1 } }"
        rel="next"
        v-if="hasNextPage"
        >Next &#62;</router-link>
    </div>
  </div>
</template>

<style scoped>
.events {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.pagination {
  display: flex;
  width: 290px;
  text-align: center;
}
.pagination a {
  flex: 1;
  text-decoration: none;
  color: #2c3e50;
}

#page-prev {
  text-align: left;
}

#page-next {
  text-align: right;
}
</style>
