<template>
  <div v-if="isOpen" class="modal-overlay" @click.self="closeModal">
    <div class="modal-card">
      <div class="modal-header">
        <div>
          <h2>Reserve a Space</h2>
          <p>Submit room details to schedule your meeting</p>
        </div>
        <button class="btn-close" @click="closeModal">&times;</button>
      </div>

      <form @submit.prevent="handleSubmit" class="booking-form">
        <div class="form-group">
          <label for="location">Meeting Location</label>
          <select id="location" v-model="form.location" required @change="form.room_name = ''">
            <option value="" disabled>Select campus location</option>
            <option v-for="loc in locations" :key="loc.id" :value="loc.name">
              {{ loc.name }} ({{ loc.city }})
            </option>
          </select>
        </div>

        <div class="form-group">
          <label for="room_name">Specific Room</label>
          <select id="room_name" v-model="form.room_name" :disabled="!form.location" required>
            <option value="" disabled>Choose an available room</option>
            <option 
              v-for="room in availableRooms" 
              :key="room" 
              :value="room"
            >
              {{ room }}
            </option>
          </select>
        </div>

        <div class="form-group">
          <label for="booked_by">Reserved By</label>
          <input 
            id="booked_by" 
            type="text" 
            v-model="form.booked_by" 
            placeholder="e.g., Alex Johnson" 
            required 
          />
        </div>

        <div class="form-group">
          <label for="booking_date">Date</label>
          <input 
            id="booking_date" 
            type="date" 
            v-model="form.booking_date" 
            required 
          />
        </div>

        <div class="form-row">
          <div class="form-group">
            <label for="start_time">Start Time</label>
            <input 
              id="start_time" 
              type="time" 
              v-model="form.start_time" 
              required 
            />
          </div>
          <div class="form-group">
            <label for="end_time">End Time</label>
            <input 
              id="end_time" 
              type="time" 
              v-model="form.end_time" 
              required 
            />
          </div>
        </div>

        <div class="form-group">
          <label for="purpose">Purpose / Agenda</label>
          <textarea 
            id="purpose" 
            v-model="form.purpose" 
            placeholder="Brief description of the event..." 
            rows="3"
          ></textarea>
        </div>

        <div class="modal-actions">
          <button type="button" class="btn-cancel" @click="closeModal">Cancel</button>
          <button type="submit" class="btn-submit" :disabled="submitting">
            {{ submitting ? 'Saving...' : 'Confirm Reservation' }}
          </button>
        </div>
      </form>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const props = defineProps({
  isOpen: Boolean,
  locations: Array
})

const emit = defineEmits(['close', 'add-booking'])

const submitting = ref(false)
const form = ref({
  location: '',
  room_name: '',
  booked_by: '',
  booking_date: '',
  start_time: '',
  end_time: '',
  purpose: ''
})

const availableRooms = computed(() => {
  const loc = props.locations.find(l => l.name === form.value.location)
  return loc ? loc.rooms : []
})

const closeModal = () => {
  emit('close')
}

const handleSubmit = () => {
  submitting.value = true
  emit('add-booking', { ...form.value })
  
  form.value = {
    location: '',
    room_name: '',
    booked_by: '',
    booking_date: '',
    start_time: '',
    end_time: '',
    purpose: ''
  }
  submitting.value = false
  closeModal()
}
</script>

<style scoped>
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background: rgba(15, 23, 42, 0.6);
  backdrop-filter: blur(4px);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 9999;
}

.modal-card {
  background: #ffffff;
  border-radius: 16px;
  width: 90%;
  max-width: 500px;
  padding: 1.75rem;
  box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.2);
  border: 1px solid #e2e8f0;
}

.modal-header {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  margin-bottom: 1.25rem;
}

.modal-header h2 {
  font-size: 1.25rem;
  font-weight: 700;
  color: #0f172a;
}

.modal-header p {
  font-size: 0.85rem;
  color: #64748b;
  margin-top: 0.2rem;
}

.btn-close {
  background: transparent;
  border: none;
  font-size: 1.5rem;
  line-height: 1;
  color: #94a3b8;
  cursor: pointer;
}

.booking-form {
  display: flex;
  flex-direction: column;
  gap: 0.9rem;
}

.form-group {
  display: flex;
  flex-direction: column;
  gap: 0.35rem;
}

.form-row {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 0.75rem;
}

label {
  font-size: 0.75rem;
  font-weight: 700;
  color: #334155;
  text-transform: uppercase;
  letter-spacing: 0.04em;
}

input, select, textarea {
  width: 100%;
  padding: 0.6rem 0.8rem;
  border: 1.5px solid #cbd5e1;
  border-radius: 8px;
  font-size: 0.9rem;
  background-color: #f8fafc;
}

input:focus, select:focus, textarea:focus {
  outline: none;
  border-color: #4f46e5;
  background-color: #ffffff;
}

.modal-actions {
  display: flex;
  justify-content: flex-end;
  gap: 0.75rem;
  margin-top: 0.75rem;
}

.btn-cancel {
  padding: 0.6rem 1.1rem;
  border: 1px solid #cbd5e1;
  background: #ffffff;
  border-radius: 8px;
  color: #475569;
  font-weight: 600;
  cursor: pointer;
}

.btn-submit {
  padding: 0.6rem 1.2rem;
  border: none;
  border-radius: 8px;
  background: linear-gradient(135deg, #4f46e5 0%, #3730a3 100%);
  color: #ffffff;
  font-weight: 600;
  cursor: pointer;
}
</style>