<template>
  <div class="card">
    <h2>Current Bookings</h2>
    <div v-if="bookings.length === 0" class="empty-state">
      No room bookings found. Create one using the form!
    </div>
    <div v-else class="table-container">
      <table>
        <thead>
          <tr>
            <th>Room</th>
            <th>Booked By</th>
            <th>Date</th>
            <th>Time</th>
            <th>Purpose</th>
            <th>Status</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="b in bookings" :key="b.id">
            <td><strong>{{ b.room_name }}</strong></td>
            <td>{{ b.booked_by }}</td>
            <td>{{ b.booking_date }}</td>
            <td>{{ b.start_time }} - {{ b.end_time }}</td>
            <td>{{ b.purpose || '—' }}</td>
            <td>
              <span :class="['status-badge', b.status || 'confirmed']">
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
defineProps({
  bookings: {
    type: Array,
    required: true
  }
})
</script>

<style scoped>
.card {
  background: #ffffff;
  padding: 1.5rem;
  border-radius: 8px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}
.empty-state {
  color: #666;
  text-align: center;
  padding: 2rem 0;
}
.table-container {
  overflow-x: auto;
}
table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 1rem;
}
th, td {
  padding: 0.75rem;
  text-align: left;
  border-bottom: 1px solid #eee;
}
th {
  background-color: #f8f9fa;
  font-weight: 600;
}
.status-badge {
  padding: 0.25rem 0.5rem;
  border-radius: 12px;
  font-size: 0.8rem;
  font-weight: bold;
  text-transform: capitalize;
}
.status-badge.confirmed {
  background-color: #e6f4ea;
  color: #137333;
}
</style>