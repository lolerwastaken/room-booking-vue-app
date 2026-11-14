<template>
  <div class="layout-wrapper">
    <nav class="navbar">
      <div class="nav-left">
        <div class="nav-brand" @click="currentPage = 'dashboard'">
          <img src="./assets/7080840.png" class="web-logo" />
          <link rel="icon" type="image/x-icon" href="/favicon.ico" />
          <span class="web-name">CUreserve</span>

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
            Locations
          </button>
        </div>
      </div>

      <div class="nav-right">
        <button class="btn-create" @click="isModalOpen = true">
          + Reserve
        </button>
      </div>
    </nav>

    <main class="dashboard-container">
      <div v-if="currentPage === 'dashboard'">
        <header class="dashboard-hero">
          <h1>Welcome to CUreserve!</h1>
          <p>Browse active room bookings, check space availability, and reserve meeting rooms across several facilities within Chulalongkorn University.</p>
        </header>

        <BookingList :bookings="bookings" />
      </div>

      <MeetingLocations 
        v-else-if="currentPage === 'locations'" 
        :locations="locations" 
      />
    </main>

    <BookingForm 
      :is-open="isModalOpen" 
      :locations="locations" 
      @close="isModalOpen = false" 
      @add-booking="handleBookingSubmit" 
    />
  </div>
</template>

<script setup>

import { ref, onMounted } from 'vue'
import BookingForm from './components/BookingForm.vue'
import BookingList from './components/BookingList.vue'
import MeetingLocations from './components/MeetingLocations.vue'
import { supabase } from './lib/supabaseClient'

const currentPage = ref('dashboard')
const isModalOpen = ref(false)

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
  },
  {
    id: 3,
    name: 'Library & Learning Commons',
    city: 'Bangkok',
    address: 'Phaya Thai Rd, Pathum Wan, Bangkok 10330',
    description: 'Quiet study environment with private rooms and collaborative spaces.',
    image: 'https://images.unsplash.com/photo-1503676260728-1c00da094a0b?auto=format&fit=crop&w=600&q=80',
    rooms: ['Study Room 1', 'Study Room 2', 'Group Study Area']
  }
])

const bookings = ref([
  {
    id: 1,
    location: 'Engineering Innovation Center',
    room_name: 'Meeting Room A',
    booked_by: 'Gerard Way',
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
  color: #fd62fd;
  cursor: pointer;
  letter-spacing: 0.5px;
  text-shadow: 0 1px 2px rgba(165, 165, 165, 0.604);
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
  padding: 0.65rem 1.2rem;
  border-radius: 8px;
  font-weight: 600;
  font-size: 0.875rem;
  cursor: pointer;
  box-shadow: 0 2px 4px rgba(79, 70, 229, 0.2);
  transition: transform 0.15s ease, box-shadow 0.15s ease;
}

.btn-create:hover {
  transform: translateY(-1px);
  box-shadow: 0 4px 8px rgba(79, 70, 229, 0.3);
}

.dashboard-container {
  max-width: 1500px;
  margin: 0 auto;
  padding: 2rem 1.5rem;
}

.web-logo {
  width: 50px;
  height: 50px;
}

.dashboard-hero {
  text-align: center;
  max-width: 640px;
  margin: 0.5rem auto 2.25rem auto;
}

.dashboard-hero h1 {
  font-size: 3.25rem;
  font-weight: 800;
  color: #fd62fd;
  letter-spacing: -0.025em;
  line-height: 1.2;
  text-shadow: 0 1px 2px rgba(165, 165, 165, 0.604);
}

.dashboard-hero p {
  font-size: 1rem;
  color: #57667a;
  margin-top: 0.5rem;
  line-height: 1.5;
}

</style>