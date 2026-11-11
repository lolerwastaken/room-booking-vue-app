<template>
  <div class="card">
    <h2>Book a Room</h2>
    <form @submit.prevent="handleSubmit">
      <div class="form-group">
        <label for="room_name">Room Name / ID:</label>
        <select id="room_name" v-model="form.room_name" required>
          <option value="" disabled>Select a room</option>
          <option value="Meeting Room A">Meeting Room A</option>
          <option value="Meeting Room B">Meeting Room B</option>
          <option value="Auditorium">Auditorium</option>
          <option value="Study Pod 1">Study Pod 1</option>
        </select>
      </div>

      <div class="form-group">
        <label for="booked_by">Your Name:</label>
        <input 
          id="booked_by" 
          type="text" 
          v-model="form.booked_by" 
          placeholder="e.g. John Doe" 
          required 
        />
      </div>

      <div class="form-group">
        <label for="booking_date">Date:</label>
        <input 
          id="booking_date" 
          type="date" 
          v-model="form.booking_date" 
          required 
        />
      </div>

      <div class="form-row">
        <div class="form-group">
          <label for="start_time">Start Time:</label>
          <input 
            id="start_time" 
            type="time" 
            v-model="form.start_time" 
            required 
          />
        </div>
        <div class="form-group">
          <label for="end_time">End Time:</label>
          <input 
            id="end_time" 
            type="time" 
            v-model="form.end_time" 
            required 
          />
        </div>
      </div>

      <div class="form-group">
        <label for="purpose">Purpose:</label>
        <textarea 
          id="purpose" 
          v-model="form.purpose" 
          placeholder="e.g. Group study session" 
          rows="3"
        ></textarea>
      </div>

      <button type="submit" :disabled="submitting">
        {{ submitting ? 'Submitting...' : 'Submit Booking' }}
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
  // Emit event to parent (App.vue) to handle local state & Supabase call
  emit('add-booking', { ...form.value })
  
  // Reset form
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
.card {
  background: #ffffff;
  padding: 1.5rem;
  border-radius: 8px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}
.form-group {
  margin-bottom: 1rem;
  display: flex;
  flex-direction: column;
}
.form-row {
  display: flex;
  gap: 1rem;
}
.form-row .form-group {
  flex: 1;
}
label {
  font-weight: 600;
  margin-bottom: 0.3rem;
  font-size: 0.9rem;
}
input, select, textarea {
  padding: 0.5rem;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 1rem;
}
button {
  width: 100%;
  padding: 0.75rem;
  background-color: #0066cc;
  color: white;
  border: none;
  border-radius: 4px;
  font-weight: bold;
  cursor: pointer;
}
button:hover {
  background-color: #004d99;
}
</style>