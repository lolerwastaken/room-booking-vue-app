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

      <form @submit.prevent="handleSubmit" class="booking-grid-form">
        <!-- Left Side: Large Image Upload Square -->
        <div class="image-upload-section">
          <label class="upload-label">Attachment Preview</label>
          <div 
            class="image-square-dropzone" 
            @click="triggerFileInput"
          >
            <img 
              v-if="imagePreview" 
              :src="imagePreview" 
              alt="Attachment preview" 
              class="uploaded-square-img" 
            />
            <div v-else class="upload-placeholder">
              <div class="upload-icon-circle">
                <svg width="28" height="28" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                  <rect x="3" y="3" width="18" height="18" rx="2" ry="2"></rect>
                  <circle cx="8.5" cy="8.5" r="1.5"></circle>
                  <polyline points="21 15 16 10 5 21"></polyline>
                </svg>
              </div>
              <span class="upload-title">Click to Upload Image</span>
              <span class="upload-subtitle">PNG, JPG, or GIF (up to 5MB)</span>
            </div>
            <button 
              v-if="imagePreview" 
              type="button" 
              class="btn-remove-square" 
              @click.stop="removeImage"
            >
              &times;
            </button>
          </div>
          <input 
            ref="fileInputRef" 
            type="file" 
            accept="image/*" 
            class="hidden-file-input" 
            @change="handleImageUpload" 
          />
        </div>

        <!-- Right Side: Booking Details Form -->
        <div class="form-fields-section">
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
              rows="2"
            ></textarea>
          </div>

          <div class="modal-actions">
            <button type="button" class="btn-cancel" @click="closeModal">Cancel</button>
            <button type="submit" class="btn-submit" :disabled="submitting">
              {{ submitting ? 'Saving...' : 'Confirm Reservation' }}
            </button>
          </div>
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

const fileInputRef = ref(null)
const submitting = ref(false)
const imagePreview = ref(null)

const form = ref({
  location: '',
  room_name: '',
  booked_by: '',
  booking_date: '',
  start_time: '',
  end_time: '',
  purpose: '',
  image_url: ''
})

const availableRooms = computed(() => {
  const loc = props.locations.find(l => l.name === form.value.location)
  return loc ? loc.rooms : []
})

const triggerFileInput = () => {
  fileInputRef.value?.click()
}

const handleImageUpload = (event) => {
  const file = event.target.files[0]
  if (file) {
    const reader = new FileReader()
    reader.onload = (e) => {
      imagePreview.value = e.target.result
      form.value.image_url = e.target.result
    }
    reader.readAsDataURL(file)
  }
}

const removeImage = () => {
  imagePreview.value = null
  form.value.image_url = ''
  if (fileInputRef.value) fileInputRef.value.value = ''
}

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
    purpose: '',
    image_url: ''
  }
  removeImage()
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
  width: 92%;
  max-width: 820px;
  max-height: 90vh;
  overflow-y: auto;
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

.booking-grid-form {
  display: grid;
  grid-template-columns: 260px 1fr;
  gap: 1.5rem;
}

@media (max-width: 700px) {
  .booking-grid-form {
    grid-template-columns: 1fr;
  }
}

.image-upload-section {
  display: flex;
  flex-direction: column;
  gap: 0.35rem;
}

.upload-label {
  font-size: 0.75rem;
  font-weight: 700;
  color: #334155;
  text-transform: uppercase;
  letter-spacing: 0.04em;
}

.image-square-dropzone {
  position: relative;
  width: 100%;
  aspect-ratio: 1 / 1;
  border: 2px dashed #cbd5e1;
  border-radius: 12px;
  background-color: #f8fafc;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  overflow: hidden;
  transition: all 0.2s ease;
}

.image-square-dropzone:hover {
  border-color: #4f46e5;
  background-color: #f0fdf4;
}

.uploaded-square-img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.upload-placeholder {
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
  padding: 1rem;
}

.upload-icon-circle {
  width: 52px;
  height: 52px;
  border-radius: 50%;
  background-color: #e0e7ff;
  color: #4f46e5;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-bottom: 0.75rem;
}

.upload-title {
  font-size: 0.85rem;
  font-weight: 600;
  color: #1e293b;
}

.upload-subtitle {
  font-size: 0.75rem;
  color: #94a3b8;
  margin-top: 0.25rem;
}

.btn-remove-square {
  position: absolute;
  top: 8px;
  right: 8px;
  background: rgba(15, 23, 42, 0.75);
  color: #ffffff;
  border: none;
  border-radius: 50%;
  width: 26px;
  height: 26px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 1rem;
}

.hidden-file-input {
  display: none;
}

.form-fields-section {
  display: flex;
  flex-direction: column;
  gap: 0.85rem;
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
  margin-top: 0.5rem;
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