<script setup>
defineProps({ application: { type: Object, required: true } })
const emit = defineEmits(['edit', 'delete'])
function formatDate(value) {
  if (!value) return 'Not set'
  return new Date(`${value}T00:00:00`).toLocaleDateString(undefined, { year: 'numeric', month: 'short', day: 'numeric' })
}
</script>

<template>
  <article class="job-card">
    <div class="company-logo">{{ application.company.slice(0, 2).toUpperCase() }}</div>
    <div class="job-main">
      <div class="job-title-row"><div><h3>{{ application.position }}</h3><p class="company">{{ application.company }}</p></div><span :class="['status', application.status.toLowerCase()]">{{ application.status }}</span></div>
      <div class="details"><span>📍 {{ application.location || 'Location not set' }}</span><span>📅 Applied {{ formatDate(application.applicationDate) }}</span><span v-if="application.salary">💰 {{ application.salary }}</span></div>
      <div v-if="application.interviewDate" class="interview"><strong>🎯 Interview:</strong> {{ formatDate(application.interviewDate) }}<span v-if="application.interviewType"> • {{ application.interviewType }}</span></div>
      <p v-if="application.notes" class="notes">{{ application.notes }}</p>
    </div>
    <div class="job-actions"><button class="edit-btn" @click="emit('edit', application)">Edit</button><button class="delete-btn" @click="emit('delete', application.id)">Delete</button></div>
  </article>
</template>
