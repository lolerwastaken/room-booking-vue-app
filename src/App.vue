<template>
  <div class="app-container">
    <header>
      <h1>Room Booking System</h1>
      <p>ISE Vue 3 & Supabase Assignment Scaffold</p>
    </header>

    <main class="grid-layout">
      <section class="form-section">
        <BookingForm @add-booking="handleBookingSubmit" />
      </section>
      <section class="list-section">
        <BookingList :bookings="bookings" />
      </section>
    </main>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import BookingForm from './components/BookingForm.vue'
import BookingList from './components/BookingList.vue'
import { supabase } from './lib/supabaseClient'

// Local state holds bookings (initialized with sample data for UI verification)
const bookings = ref([
  {
    id: 1,
    room_name: 'Meeting Room A',
    booked_by: 'Alex Smith',
    booking_date: '2026-11-20',
    start_time: '10:00',
    end_time: '12:00',
    purpose: 'Team Sync',
    status: 'confirmed'
  }
])

// Fetch records from Supabase primary table
const fetchBookings = async () => {
  try {
    const { data, error } = await supabase
      .from('bookings')
      .select('*')
      .order('created_at', { ascending: false })

    if (error) throw error
    if (data && data.length > 0) {
      bookings.value = data
    }
  } catch (err) {
    console.warn('Supabase not connected yet or table empty. Showing local state.', err.message)
  }
}

// Add record to Supabase primary table
const handleBookingSubmit = async (newBooking) => {
  // 1. Optimistic UI update locally
  const tempRecord = { id: Date.now(), ...newBooking, status: 'confirmed' }
  bookings.value.unshift(tempRecord)

  // 2. Insert into Supabase primary table
  try {
    const { data, error } = await supabase
      .from('bookings')
      .insert([newBooking])
      .select()

    if (error) throw error
    console.log('Successfully inserted into Supabase:', data)
  } catch (err) {
    console.warn('Record added locally. Connect Supabase credentials to persist database rows.', err.message)
  }
}

onMounted(() => {
  fetchBookings()
})
</script>

<style>
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}
body {
  background-color: #f4f6f9;
  color: #333;
}
.app-container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 2rem 1rem;
}
header {
  margin-bottom: 2rem;
}
header h1 {
  color: #1a252f;
}
header p {
  color: #666;
}
.grid-layout {
  display: grid;
  grid-template-columns: 1fr;
  gap: 2rem;
}
@media (min-width: 768px) {
  .grid-layout {
    grid-template-columns: 350px 1fr;
  }
}
</style>