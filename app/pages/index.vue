<template>
  <div class="min-h-screen bg-gray-50 p-4 flex justify-center items-start">
    <div class="max-w-3xl w-full">
      <!-- Form -->
      <div class="bg-white rounded-lg shadow-sm border border-gray-200 p-8">
        <!-- Header inside form -->
        <div class="text-center mb-8">
          <h1 class="text-2xl font-bold text-black mb-3">INCIDENT REPORT FORM</h1>
          <p class="text-sm text-black leading-relaxed">We are happy that you reported this, but far from happy that this happened to you.</p>
        </div>
        
        <form @submit.prevent="submitForm" class="flex flex-col gap-7">
          <!-- What Happened Section -->
          <div class="flex flex-col gap-4">
            <label class="text-lg font-semibold text-black after:content-['*'] after:text-red-500 after:ml-1">What Happened:</label>
            <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-3">
              <div class="flex items-center gap-2 p-2.5 border border-gray-200 rounded hover:bg-gray-50 transition-colors" v-for="option in incidentTypes" :key="option.value">
                <input 
                  type="checkbox" 
                  :id="option.value" 
                  :value="option.value" 
                  v-model="form.whatHappened"
                  class="w-4 h-4 text-blue-600 accent-blue-600 rounded"
                >
                <label :for="option.value" class="text-sm text-black cursor-pointer flex-1">{{ option.label }}</label>
              </div>
            </div>
          </div>

          <!-- Describe the incident -->
          <div class="flex flex-col gap-4">
            <label for="description" class="text-lg font-semibold text-black">Describe the incident:</label>
            <textarea 
              id="description"
              v-model="form.description"
              placeholder="Please provide details about what happened..."
              class="px-3 py-2.5 border border-gray-300 rounded text-sm text-black transition-colors focus:outline-none focus:border-blue-600 focus:ring-2 focus:ring-blue-100 resize-none"
              rows="5"
            ></textarea>
          </div>

          <!-- When did it happen -->
          <div class="flex flex-col gap-4">
            <label for="datetime" class="text-lg font-semibold text-black after:content-['*'] after:text-red-500 after:ml-1">When did it happen:</label>
            <input 
              type="datetime-local" 
              id="datetime"
              v-model="form.datetime"
              class="px-3 py-2.5 border border-gray-300 rounded text-sm text-black transition-colors focus:outline-none focus:border-blue-600 focus:ring-2 focus:ring-blue-100"
              required
            >
          </div>

          <!-- Where did it happen -->
          <div class="flex flex-col gap-4">
            <div class="flex items-center justify-between">
              <label class="text-lg font-semibold text-black after:content-['*'] after:text-red-500 after:ml-1">Where did it happen:</label>
              <button 
                type="button"
                @click="getCurrentLocation"
                :disabled="isGettingLocation"
                class="px-3 py-1.5 bg-green-600 hover:bg-green-700 disabled:bg-gray-400 disabled:cursor-not-allowed text-white rounded text-xs font-medium transition-colors whitespace-nowrap flex items-center gap-1.5"
              >
                <svg v-if="isGettingLocation" class="animate-spin h-3 w-3" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
                  <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                  <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
                </svg>
                <svg v-else class="h-3 w-3" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17.657 16.657L13.414 20.9a1.998 1.998 0 01-2.827 0l-4.244-4.243a8 8 0 1111.314 0z"></path>
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 11a3 3 0 11-6 0 3 3 0 016 0z"></path>
                </svg>
                {{ isGettingLocation ? 'Getting...' : 'Get Location' }}
              </button>
            </div>
            
            <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
              <!-- Street Name -->
              <div class="flex flex-col gap-2">
                <label for="street" class="text-sm font-medium text-black">Street Name</label>
                <input 
                  type="text" 
                  id="street"
                  v-model="form.street"
                  placeholder="E.g. Main Street"
                  class="px-3 py-2 border border-gray-300 rounded text-sm text-black transition-colors focus:outline-none focus:border-blue-600 focus:ring-1 focus:ring-blue-100"
                  required
                >
              </div>
              
              <!-- House Number -->
              <div class="flex flex-col gap-2">
                <label for="houseNumber" class="text-sm font-medium text-black">House Number</label>
                <input 
                  type="text" 
                  id="houseNumber"
                  v-model="form.houseNumber"
                  placeholder="E.g. 123A"
                  class="px-3 py-2 border border-gray-300 rounded text-sm text-black transition-colors focus:outline-none focus:border-blue-600 focus:ring-1 focus:ring-blue-100"
                  required
                >
              </div>
              
              <!-- Postal Code -->
              <div class="flex flex-col gap-2">
                <label for="postcode" class="text-sm font-medium text-black">Postal Code</label>
                <input 
                  type="text" 
                  id="postcode"
                  v-model="form.postcode"
                  placeholder="E.g. 1012 AB"
                  class="px-3 py-2 border border-gray-300 rounded text-sm text-black transition-colors focus:outline-none focus:border-blue-600 focus:ring-1 focus:ring-blue-100"
                  required
                >
              </div>
              
              <!-- City -->
              <div class="flex flex-col gap-2">
                <label for="city" class="text-sm font-medium text-black">City</label>
                <input 
                  type="text" 
                  id="city"
                  v-model="form.city"
                  placeholder="E.g. Amsterdam"
                  class="px-3 py-2 border border-gray-300 rounded text-sm text-black transition-colors focus:outline-none focus:border-blue-600 focus:ring-1 focus:ring-blue-100"
                  required
                >
              </div>
            </div>
            
            <p v-if="locationError" class="text-red-600 text-xs">{{ locationError }}</p>
          </div>

          <!-- Submit Button -->
          <div class="flex justify-center pt-4">
            <button 
              type="submit" 
              class="bg-blue-600 hover:bg-blue-700 disabled:opacity-60 disabled:cursor-not-allowed text-white px-10 py-3 rounded text-base font-semibold cursor-pointer transition-colors"
              :disabled="!isFormValid"
            >
              Submit Report
            </button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const form = ref({
  whatHappened: [],
  description: '',
  datetime: '',
  street: '',
  houseNumber: '',
  postcode: '',
  city: ''
})

const isGettingLocation = ref(false)
const locationError = ref('')

const incidentTypes = [
  { value: 'sissing', label: 'Hissing' },
  { value: 'screaming', label: 'Screaming/Insulting' },
  { value: 'intimidation', label: 'Intimidation' },
  { value: 'touching', label: 'Touching' },
  { value: 'punching', label: 'Punching/Kicking' },
  { value: 'pushing', label: 'Pushing/Dragging' }
]

const isFormValid = computed(() => {
  return form.value.whatHappened.length > 0 && 
         form.value.datetime && 
         form.value.street.trim() &&
         form.value.houseNumber.trim() &&
         form.value.postcode.trim() &&
         form.value.city.trim()
})

const getCurrentLocation = () => {
  if (!navigator.geolocation) {
    locationError.value = 'Geolocation is not supported by this browser.'
    return
  }

  isGettingLocation.value = true
  locationError.value = ''

  navigator.geolocation.getCurrentPosition(
    async (position) => {
      try {
        const { latitude, longitude } = position.coords
        
        // Use Nominatim (OpenStreetMap) reverse geocoding service - free and no API key needed
        // zoom=18 for more precise results, extratags=1 for additional data
        const response = await fetch(
          `https://nominatim.openstreetmap.org/reverse?format=json&lat=${latitude}&lon=${longitude}&addressdetails=1&extratags=1&zoom=18&accept-language=en`
        )
        
        if (response.ok) {
          const data = await response.json()
          
          if (data && data.address) {
            const address = data.address
            
            // Extract address components with multiple fallbacks
            const street = address.road || address.street || address.pedestrian || address.path || ''
            
            // Try multiple ways to get house number
            let houseNumber = address.house_number || ''
            if (!houseNumber) {
              // Sometimes it's in the display_name or other fields
              const displayName = data.display_name || ''
              const houseNumberMatch = displayName.match(/^(\d+[a-zA-Z]?)\s/)
              if (houseNumberMatch) {
                houseNumber = houseNumberMatch[1]
              }
            }
            
            const postcode = address.postcode || address.postal_code || ''
            const city = address.city || address.town || address.village || address.municipality || address.hamlet || ''
            
            // Format the address
            let formattedAddress = ''
            
            if (street) {
              formattedAddress += street
              if (houseNumber) {
                formattedAddress += ` ${houseNumber}`
              }
            }
            
            if (postcode && city) {
              if (formattedAddress) formattedAddress += ', '
              formattedAddress += `${postcode} ${city}`
            } else if (city) {
              if (formattedAddress) formattedAddress += ', '
              formattedAddress += city
            }
            
            // Fill in the separate address fields
            form.value.street = street
            form.value.houseNumber = houseNumber
            form.value.postcode = postcode
            form.value.city = city
          } else {
            // Fallback to coordinates in street field if no address data
            form.value.street = `${latitude.toFixed(6)}, ${longitude.toFixed(6)}`
            form.value.houseNumber = ''
            form.value.postcode = ''
            form.value.city = ''
          }
        } else {
          // Fallback to coordinates if API fails
          form.value.street = `${latitude.toFixed(6)}, ${longitude.toFixed(6)}`
          form.value.houseNumber = ''
          form.value.postcode = ''
          form.value.city = ''
        }
      } catch (error) {
        // Fallback to coordinates if anything fails
        const { latitude, longitude } = position.coords
        form.value.street = `${latitude.toFixed(6)}, ${longitude.toFixed(6)}`
        form.value.houseNumber = ''
        form.value.postcode = ''
        form.value.city = ''
      } finally {
        isGettingLocation.value = false
      }
    },
    (error) => {
      isGettingLocation.value = false
      switch (error.code) {
        case error.PERMISSION_DENIED:
          locationError.value = 'Location access denied by user. Please allow location access and try again.'
          break
        case error.POSITION_UNAVAILABLE:
          locationError.value = 'Location information is unavailable. Please enter your location manually.'
          break
        case error.TIMEOUT:
          locationError.value = 'Location request timed out. Please try again or enter your location manually.'
          break
        default:
          locationError.value = 'An unknown error occurred while retrieving location. Please try again.'
          break
      }
    },
    {
      enableHighAccuracy: true,
      timeout: 10000,
      maximumAge: 300000 // 5 minutes
    }
  )
}

const submitForm = () => {
  if (isFormValid.value) {
    console.log('Form submitted:', form.value)
    // Here you would typically send the data to your backend
    alert('Thank you for your report. We take this seriously and will review your submission.')
    
    // Reset form
    form.value = {
      whatHappened: [],
      description: '',
      datetime: '',
      street: '',
      houseNumber: '',
      postcode: '',
      city: ''
    }
    locationError.value = ''
  }
}
</script>


  