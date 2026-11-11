<template>
  <div class="layout-wrapper">
    <!-- Top Header Navigation Bar -->
    <nav class="navbar">
      <div class="nav-left">
        <div class="nav-brand" @click="currentPage = 'dashboard'">
          <span class="logo-icon">🏢</span>
          <span class="brand-name">ReserveHub</span>
        </div>
        <div class="nav-links">
          <button 
            :class="['nav-link', { active: currentPage === 'dashboard' }]"
            @click="currentPage = 'dashboard'"
          >
            Dashboard
          </button>
          <button 
            :class="['nav-link', { active: currentPage === 'locations' }]"
            @click="currentPage = 'locations'"
          >
            Locations & Rooms
          </button>
        </div>
      </div>

      <div class="nav-right">
        <button class="btn-create" @click="isModalOpen = true">
          <span>+ Create Reservation</span>
        </button>
      </div>
    </nav>

    <!-- Main View Switcher -->
    <main class="dashboard-container">
      <BookingList 
        v-if="currentPage === 'dashboard'" 
        :bookings="bookings" 
      />

      <LocationCatalog 
        v-else-if="currentPage === 'locations'" 
        :locations="locations" 
      />
    </main>

    <!-- Room Booking Modal Popup -->
    <BookingFormModal 
      :is-open="isModalOpen" 
      :locations="locations" 
      @close="isModalOpen = false" 
      @add-booking="handleBookingSubmit" 
    />
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import BookingFormModal from './components/BookingFormModal.vue'
import BookingList from './components/BookingList.vue'
import LocationCatalog from './components/LocationCatalog.vue'
import { supabase } from './lib/supabaseClient'

const currentPage = ref('dashboard') // 'dashboard' | 'locations'
const isModalOpen = ref(false)

// Sample Meeting Locations Dataset
const locations = ref([
  {
    id: 1,
    name: 'Engineering Innovation Center',
    city: 'Bangkok',
    address: '254 Phaya Thai Rd, Pathum Wan, Bangkok 10330',
    description: 'Modern collaborative space featuring high-tech presentation setups and pod areas.',
    image: 'https://images.unsplash.com/photo-1497366216548-37526070297c?auto=format&fit=crop&w=600&q=80',
    rooms: ['Meeting Room A', 'Meeting Room B', 'Study Pod 1']
  },
  {
    id: 2,
    name: 'Central Academic Auditorium Complex',
    city: 'Bangkok',
    address: 'Henri Dunant Rd, Pathum Wan, Bangkok 10330',
    description: 'Large capacity lecture hall designed for seminars, keynotes, and events.',
    image: 'https://images.unsplash.com/photo-1431540015161-0bf868a2d407?auto=format&fit=crop&w=600&q=80',
    rooms: ['Main Auditorium', 'Seminar Room 101']
  }
])

const bookings = ref([
  {
    id: 1,
    location: 'Engineering Innovation Center',
    room_name: 'Meeting Room A',
    booked_by: 'Alex Smith',
    booking_date: '2026-11-20',
    start_time: '10:00',
    end_time: '12:00',
    purpose: 'Team Architecture Sync',
    status: 'confirmed'
  }
])

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
    console.warn('Database unconfigured or offline. Running on local state.', err.message)
  }
}

const handleBookingSubmit = async (newBooking) => {
  const tempRecord = { id: Date.now(), ...newBooking, status: 'confirmed' }
  bookings.value.unshift(tempRecord)

  try {
    const { data, error } = await supabase
      .from('bookings')
      .insert([newBooking])
      .select()

    if (error) throw error
    console.log('Record inserted into Supabase:', data)
  } catch (err) {
    console.warn('Record stored locally.', err.message)
  }
}

onMounted(() => {
  fetchBookings()
})
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap');

* { box-sizing: border-box; margin: 0; padding: 0; }
body {
  font-family: 'Inter', sans-serif;
  background-color: #f1f5f9;
  color: #0f172a;
}

.layout-wrapper {
  min-height: 100vh;
}

.navbar {
  height: 64px;
  background-color: #ffffff;
  border-bottom: 1px solid #e2e8f0;
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0 2rem;
}

.nav-left {
  display: flex;
  align-items: center;
  gap: 2rem;
}

.nav-brand {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  font-weight: 700;
  font-size: 1.15rem;
  color: #4f46e5;
  cursor: pointer;
}

.nav-links {
  display: flex;
  gap: 1rem;
}

.nav-link {
  background: none;
  border: none;
  font-size: 0.9rem;
  font-weight: 600;
  color: #64748b;
  cursor: pointer;
  padding: 0.5rem 0.75rem;
  border-radius: 6px;
  transition: all 0.15s ease;
}

.nav-link:hover, .nav-link.active {
  color: #4f46e5;
  background-color: #f1f5f9;
}

.btn-create {
  background: linear-gradient(135deg, #4f46e5 0%, #3730a3 100%);
  color: #ffffff;
  border: none;
  padding: 0.6rem 1.1rem;
  border-radius: 8px;
  font-weight: 600;
  font-size: 0.875rem;
  cursor: pointer;
  box-shadow: 0 2px 4px rgba(79, 70, 229, 0.2);
}

.dashboard-container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 2rem 1.5rem;
}
</style>