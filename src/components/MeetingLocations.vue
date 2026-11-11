<template>
  <div class="catalog-container">
    <div class="catalog-header">
      <h2>Campus Meeting Locations</h2>
      <p>Explore facilities and reserved spaces available for booking</p>
    </div>

    <div class="location-grid">
      <div v-for="loc in locations" :key="loc.id" class="location-card">
        <div class="image-wrapper">
          <img :src="loc.image" :alt="loc.name" />
          <span class="badge-city">{{ loc.city }}</span>
        </div>
        <div class="card-body">
          <h3>{{ loc.name }}</h3>
          <p class="address">📍 {{ loc.address }}</p>
          <p class="description">{{ loc.description }}</p>
          
          <div class="rooms-tags">
            <span v-for="room in loc.rooms" :key="room" class="room-tag">
              {{ room }}
            </span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
defineProps({
  locations: {
    type: Array,
    required: true
  }
})
</script>

<style scoped>
.catalog-container {
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
}

.catalog-header h2 {
  font-size: 1.5rem;
  color: #0f172a;
}

.catalog-header p {
  color: #64748b;
  font-size: 0.9rem;
}

.location-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(320px, 1fr));
  gap: 1.5rem;
}

.location-card {
  background: #ffffff;
  border-radius: 12px;
  overflow: hidden;
  border: 1px solid #e2e8f0;
  box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.05);
}

.image-wrapper {
  position: relative;
  height: 180px;
}

.image-wrapper img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.badge-city {
  position: absolute;
  top: 12px;
  right: 12px;
  background: rgba(15, 23, 42, 0.75);
  color: #ffffff;
  padding: 0.25rem 0.6rem;
  border-radius: 6px;
  font-size: 0.75rem;
  font-weight: 600;
}

.card-body {
  padding: 1.25rem;
}

.card-body h3 {
  font-size: 1.15rem;
  color: #0f172a;
  margin-bottom: 0.35rem;
}

.address {
  font-size: 0.85rem;
  color: #4f46e5;
  font-weight: 500;
  margin-bottom: 0.75rem;
}

.description {
  font-size: 0.875rem;
  color: #64748b;
  margin-bottom: 1rem;
  line-height: 1.4;
}

.rooms-tags {
  display: flex;
  flex-wrap: wrap;
  gap: 0.4rem;
}

.room-tag {
  background: #f1f5f9;
  color: #334155;
  font-size: 0.75rem;
  padding: 0.25rem 0.5rem;
  border-radius: 4px;
  font-weight: 500;
}
</style>