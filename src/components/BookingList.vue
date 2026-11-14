<template>
  <div class="card list-card">
    <div class="card-header">
      <div>
        <h2>Scheduled Sessions</h2>
        <p>Active and upcoming room reservations</p>
      </div>
      <span class="badge-count">{{ bookings.length }} Total</span>
    </div>

    <div v-if="bookings.length === 0" class="empty-state">
      <div class="empty-icon">📅</div>
      <h3>No bookings yet</h3>
      <p>Click "+ Create Reservation" on the header to add your first room entry.</p>
    </div>

    <div v-else class="table-responsive">
      <table class="custom-table">
        <thead>
          <tr>
            <th> </th>
            <th>Location</th>
            <th>Room</th>
            <th>Reserved By</th>
            <th>Date & Time</th>
            <th>Purpose</th>
            <th>Status</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="b in bookings" :key="b.id">
            <td class="attachment-cell">
              <div class="thumbnail-wrapper">
                <img 
                  :src="b.image_url || defaultImage" 
                  alt="Booking thumbnail" 
                  class="thumbnail-img" 
                />
              </div>
            </td>
            <td class="location-cell">{{ b.location || 'Main Campus' }}</td>
            <td class="room-cell">
              <span class="room-pill">{{ b.room_name }}</span>
            </td>
            <td><strong>{{ b.booked_by }}</strong></td>
            <td>
              <div class="time-wrapper">
                <span class="date-text">{{ b.booking_date }}</span>
                <span class="time-text">{{ b.start_time }} – {{ b.end_time }}</span>
              </div>
            </td>
            <td class="purpose-cell">{{ b.purpose || 'N/A' }}</td>
            <td>
              <span :class="['status-pill', b.status || 'confirmed']">
                {{ b.status || 'Confirmed' }}
              </span>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script setup>
import defaultImage from '../assets/defaultImage.png'

defineProps({
  bookings: { type: Array, required: true }
})
</script>

<style scoped>
.list-card {
  background: #ffffff;
  border-radius: 16px;
  padding: 1.75rem;
  box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.05);
  border: 1px solid #e2e8f0;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1.5rem;
}

.card-header h2 { font-size: 1.35rem; font-weight: 700; color: #0f172a; }
.card-header p { font-size: 0.85rem; color: #64748b; }

.badge-count {
  background-color: #e0e7ff;
  color: #4338ca;
  font-size: 0.75rem;
  font-weight: 700;
  padding: 0.35rem 0.75rem;
  border-radius: 9999px;
}

.empty-state {
  text-align: center;
  padding: 3rem 1rem;
  background-color: #f8fafc;
  border-radius: 12px;
  border: 2px dashed #cbd5e1;
}

.empty-icon { font-size: 2.5rem; margin-bottom: 0.5rem; }

.table-responsive { overflow-x: auto; }

.custom-table { width: 100%; border-collapse: separate; border-spacing: 0; }

.custom-table th {
  background-color: #f8fafc;
  color: #475569;
  font-size: 0.75rem;
  font-weight: 700;
  text-transform: uppercase;
  padding: 0.85rem 1rem;
  border-bottom: 1px solid #e2e8f0;
  text-align: left;
}

.custom-table td { 
  padding: 1rem; 
  border-bottom: 1px solid #f1f5f9; 
  font-size: 0.9rem; 
  vertical-align: middle; 
}

.attachment-cell { 
  width: 110px; 
}

.thumbnail-wrapper {
  width: 84px;
  height: 84px;
  border-radius: 10px;
  overflow: hidden;
  border: 1px solid #e2e8f0;
  background-color: #f1f5f9;
}

.thumbnail-img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.location-cell { 
  font-weight: 600; 
  color: #4f46e5; 
}

.room-pill { 
  font-weight: 600; 
  background: #f1f5f9; 
  padding: 0.3rem 0.6rem; 
  border-radius: 6px; 
}

.time-wrapper { 
  display: flex; 
  flex-direction: column; 
}

.date-text { 
  font-weight: 600; 
}

.time-text { 
  font-size: 0.8rem; 
  color: #64748b; 
}

.purpose-cell {
  max-width: 240px;
  white-space: normal;
  word-wrap: break-word;
  overflow-wrap: anywhere;
  line-height: 1.4;
  color: #64748b;
}

.status-pill { 
  padding: 0.25rem 0.65rem; 
  border-radius: 9999px; 
  font-size: 0.75rem; 
  font-weight: 600; 
}

.status-pill.confirmed { 
  background-color: #dcfce7; 
  color: #15803d; 
  }
</style>