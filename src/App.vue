<script setup>
import { computed, onMounted, ref } from 'vue'
import JobForm from './components/JobForm.vue'
import JobItem from './components/JobItem.vue'

const applications = ref([])
const showForm = ref(false)
const editingApplication = ref(null)
const search = ref('')
const statusFilter = ref('All')

const statuses = ['Applied', 'Interview', 'Assessment', 'Rejected', 'Hired']

const emptyApplication = () => ({
  company: '',
  position: '',
  location: '',
  applicationDate: new Date().toISOString().slice(0, 10),
  status: 'Applied',
  interviewDate: '',
  interviewType: '',
  salary: '',
  notes: ''
})

function loadApplications() {
  const saved = localStorage.getItem('jobApplications')
  if (saved) {
    try { applications.value = JSON.parse(saved) } catch { applications.value = [] }
  }
}

function saveApplications() {
  localStorage.setItem('jobApplications', JSON.stringify(applications.value))
}

function addApplication(data) {
  applications.value.unshift({ id: Date.now(), ...data })
  saveApplications()
  showForm.value = false
}

function updateApplication(data) {
  const index = applications.value.findIndex(item => item.id === data.id)
  if (index !== -1) applications.value[index] = { ...data }
  saveApplications()
  editingApplication.value = null
  showForm.value = false
}

function deleteApplication(id) {
  if (!confirm('Delete this job application?')) return
  applications.value = applications.value.filter(item => item.id !== id)
  saveApplications()
}

function startEdit(application) {
  editingApplication.value = { ...application }
  showForm.value = true
  window.scrollTo({ top: 0, behavior: 'smooth' })
}

function cancelForm() {
  editingApplication.value = null
  showForm.value = false
}

function openAdd() {
  editingApplication.value = null
  showForm.value = true
  window.scrollTo({ top: 0, behavior: 'smooth' })
}

const filteredApplications = computed(() => applications.value.filter(item => {
  const text = `${item.company} ${item.position} ${item.location}`.toLowerCase()
  const matchesSearch = text.includes(search.value.toLowerCase().trim())
  const matchesStatus = statusFilter.value === 'All' || item.status === statusFilter.value
  return matchesSearch && matchesStatus
}))

const stats = computed(() => ({
  total: applications.value.length,
  interviews: applications.value.filter(x => x.status === 'Interview').length,
  hired: applications.value.filter(x => x.status === 'Hired').length,
  pending: applications.value.filter(x => ['Applied', 'Assessment'].includes(x.status)).length
}))

onMounted(loadApplications)
</script>

<template>
  <div class="app-shell">
    <header class="topbar">
      <div class="brand">
        <div class="brand-icon">JT</div>
        <div>
          <h1>JobTrack</h1>
          <span>Job Application Tracker</span>
        </div>
      </div>
      <button class="primary-btn" @click="openAdd">+ Add Application</button>
    </header>

    <main class="content">
      <section class="hero">
        <div>
          <p class="eyebrow">YOUR CAREER DASHBOARD</p>
          <h2>Track every opportunity in one place.</h2>
          <p class="hero-text">Keep your applications, interviews, assessments, and offers organized.</p>
        </div>
        <div class="hero-mark">💼</div>
      </section>

      <section class="stats-grid">
        <div class="stat-card"><span>Total Applications</span><strong>{{ stats.total }}</strong><small>All applications</small></div>
        <div class="stat-card"><span>Interviews</span><strong>{{ stats.interviews }}</strong><small>Interview stage</small></div>
        <div class="stat-card"><span>Pending</span><strong>{{ stats.pending }}</strong><small>Applied or assessment</small></div>
        <div class="stat-card"><span>Hired</span><strong>{{ stats.hired }}</strong><small>Successful applications</small></div>
      </section>

      <JobForm
        v-if="showForm"
        :application="editingApplication || emptyApplication()"
        :editing="Boolean(editingApplication)"
        @save="editingApplication ? updateApplication($event) : addApplication($event)"
        @cancel="cancelForm"
      />

      <section class="applications-section">
        <div class="section-heading">
          <div>
            <p class="eyebrow">APPLICATIONS</p>
            <h2>Your Job Applications</h2>
          </div>
          <button class="secondary-btn" @click="openAdd">+ New Application</button>
        </div>

        <div class="toolbar">
          <input v-model="search" class="search" type="search" placeholder="Search company, position, or location..." />
          <select v-model="statusFilter">
            <option>All</option>
            <option v-for="status in statuses" :key="status">{{ status }}</option>
          </select>
        </div>

        <div v-if="filteredApplications.length" class="job-list">
          <JobItem
            v-for="application in filteredApplications"
            :key="application.id"
            :application="application"
            @edit="startEdit"
            @delete="deleteApplication"
          />
        </div>

        <div v-else class="empty-state">
          <div class="empty-icon">📋</div>
          <h3>No applications found</h3>
          <p>Add your first job application to start tracking your progress.</p>
          <button class="primary-btn" @click="openAdd">Add Your First Application</button>
        </div>
      </section>
    </main>

    <footer>JobTrack • Vue CRUD Application</footer>
  </div>
</template>
