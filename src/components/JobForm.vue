<script setup>
import { reactive, watch } from 'vue'

const props = defineProps({
  application: { type: Object, required: true },
  editing: Boolean
})
const emit = defineEmits(['save', 'cancel'])

const form = reactive({ ...props.application })
watch(() => props.application, value => Object.assign(form, value), { deep: true })

function submit() {
  if (!form.company.trim() || !form.position.trim()) return
  emit('save', { ...form, company: form.company.trim(), position: form.position.trim() })
}
</script>

<template>
  <section class="form-card">
    <div class="form-header">
      <div><p class="eyebrow">{{ editing ? 'UPDATE' : 'CREATE' }}</p><h2>{{ editing ? 'Edit Application' : 'Add Job Application' }}</h2></div>
      <button class="close-btn" @click="emit('cancel')">×</button>
    </div>
    <form @submit.prevent="submit">
      <div class="form-grid">
        <label>Company Name *<input v-model="form.company" required placeholder="e.g. Google" /></label>
        <label>Job Position *<input v-model="form.position" required placeholder="e.g. Frontend Developer" /></label>
        <label>Location<input v-model="form.location" placeholder="e.g. Cebu City" /></label>
        <label>Application Date<input v-model="form.applicationDate" type="date" /></label>
        <label>Status<select v-model="form.status"><option>Applied</option><option>Interview</option><option>Assessment</option><option>Rejected</option><option>Hired</option></select></label>
        <label>Interview Date<input v-model="form.interviewDate" type="date" /></label>
        <label>Interview Type<select v-model="form.interviewType"><option value="">Select type</option><option>Phone</option><option>Video</option><option>In-person</option></select></label>
        <label>Expected Salary<input v-model="form.salary" placeholder="e.g. ₱40,000/month" /></label>
        <label class="full">Notes<textarea v-model="form.notes" rows="3" placeholder="Add notes about this application..."></textarea></label>
      </div>
      <div class="form-actions"><button type="button" class="secondary-btn" @click="emit('cancel')">Cancel</button><button class="primary-btn" type="submit">{{ editing ? 'Save Changes' : 'Save Application' }}</button></div>
    </form>
  </section>
</template>
