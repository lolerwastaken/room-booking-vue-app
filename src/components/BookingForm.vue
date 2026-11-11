<template>
  <div class="card form-card">
    <div class="card-header">
      <h2>Reserve a Space</h2>
      <p>Submit room details to schedule your meeting</p>
    </div>

    <form @submit.prevent="handleSubmit" class="booking-form">
      <div class="form-group">
        <label for="room_name">Select Room</label>
        <div class="input-wrapper">
          <select id="room_name" v-model="form.room_name" required>
            <option value="" disabled>Choose an available room</option>
            <option value="Meeting Room A">Meeting Room A (Cap: 8)</option>
            <option value="Meeting Room B">Meeting Room B (Cap: 12)</option>
            <option value="Auditorium">Auditorium (Cap: 100)</option>
            <option value="Study Pod 1">Study Pod 1 (Cap: 2)</option>
          </select>
        </div>
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
          placeholder="Brief description of the event or meeting..." 
          rows="3"
        ></textarea>
      </div>

      <button type="submit" class="btn-submit" :disabled="submitting">
        <span>{{ submitting ? 'Processing...' : 'Confirm Booking' }}</span>
      </button>
    </form>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const emit = defineEmits(['add-booking'])

const submitting = ref(false)
const form = ref({
  room_name: '',
  booked_by: '',
  booking_date: '',
  start_time: '',
  end_time: '',
  purpose: ''
})

const handleSubmit = async () => {
  submitting.value = true
  emit('add-booking', { ...form.value })
  
  form.value = {
    room_name: '',
    booked_by: '',
    booking_date: '',
    start_time: '',
    end_time: '',
    purpose: ''
  }
  submitting.value = false
}
</script>

<style scoped>
.form-card {
  background: #ffffff;
  border-radius: 16px;
  padding: 1.75rem;
  box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.05), 0 8px 10px -6px rgba(0, 0, 0, 0.01);
  border: 1px solid #e2e8f0;
}

.card-header {
  margin-bottom: 1.5rem;
}

.card-header h2 {
  font-size: 1.35rem;
  font-weight: 700;
  color: #0f172a;
}

.card-header p {
  font-size: 0.85rem;
  color: #64748b;
  margin-top: 0.25rem;
}

.booking-form {
  display: flex;
  flex-direction: column;
  gap: 1.1rem;
}

.form-group {
  display: flex;
  flex-direction: column;
  gap: 0.4rem;
}

.form-row {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 0.75rem;
}

label {
  font-size: 0.825rem;
  font-weight: 600;
  color: #334155;
  text-transform: uppercase;
  letter-spacing: 0.03em;
}

input, select, textarea {
  width: 100%;
  padding: 0.65rem 0.85rem;
  border: 1.5px solid #cbd5e1;
  border-radius: 8px;
  font-size: 0.925rem;
  color: #1e293b;
  background-color: #f8fafc;
  transition: all 0.2s ease-in-out;
}

input:focus, select:focus, textarea:focus {
  outline: none;
  border-color: #4f46e5;
  background-color: #ffffff;
  box-shadow: 0 0 0 4px rgba(79, 70, 229, 0.1);
}

.btn-submit {
  margin-top: 0.5rem;
  padding: 0.85rem;
  border: none;
  border-radius: 10px;
  background: linear-gradient(135deg, #4f46e5 0%, #3730a3 100%);
  color: #ffffff;
  font-weight: 600;
  font-size: 0.95rem;
  cursor: pointer;
  transition: transform 0.15s ease, box-shadow 0.15s ease;
}

.btn-submit:hover {
  transform: translateY(-1px);
  box-shadow: 0 4px 12px rgba(79, 70, 229, 0.35);
}

.btn-submit:active {
  transform: translateY(0);
}

.btn-submit:disabled {
  opacity: 0.65;
  cursor: not-allowed;
}
</style>