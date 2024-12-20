<script setup>
import { ref, onMounted, computed } from 'vue';
import { Head } from '@inertiajs/vue3';
import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout.vue';
import { IceCream, ChevronLeft, ChevronRight, Loader2 } from 'lucide-vue-next';


const popularFlavors = ref([]);
const isLoading = ref(true);
const error = ref(null);

const currentSlide = ref(0);
const itemsPerSlide = ref(4);

const totalSlides = computed(() => Math.ceil(popularFlavors.value.length / itemsPerSlide.value));

const fetchFlavors = async () => {
  try {
    isLoading.value = true;
    error.value = null;
    // Replace '/api/flavors' with your actual API endpoint
    const response = await route.get('/api/flavors');
    popularFlavors.value = response.data;
  } catch (err) {
    console.error('Error fetching flavors:', err);
    error.value = 'Failed to load flavors. Please try again later.';
  } finally {
    isLoading.value = false;
  }
};

const nextSlide = () => {
  currentSlide.value = (currentSlide.value + 1) % totalSlides.value;
};

const prevSlide = () => {
  currentSlide.value = (currentSlide.value - 1 + totalSlides.value) % totalSlides.value;
};

const goToSlide = (index) => {
  currentSlide.value = index;
};

onMounted(() => {
  fetchFlavors();
  const interval = setInterval(nextSlide, 5000);
  return () => clearInterval(interval);
});

// Adjust items per slide based on screen size
const handleResize = () => {
  if (window.innerWidth < 640) {
    itemsPerSlide.value = 1;
  } else if (window.innerWidth < 768) {
    itemsPerSlide.value = 2;
  } else if (window.innerWidth < 1024) {
    itemsPerSlide.value = 3;
  } else {
    itemsPerSlide.value = 4;
  }
};

onMounted(() => {
  handleResize();
  window.addEventListener('resize', handleResize);
  return () => window.removeEventListener('resize', handleResize);
});
</script>

<template>
  <Head title="Dashboard - Ice Cream Inventory" />

  <AuthenticatedLayout>
    <div class="min-h-screen bg-gradient-to-b from-[#EDCFA9] to-[#AA4A30] dark:from-[#D57149] dark:to-[#AA4A30] py-12">
      <div class="mx-auto max-w-7xl px-4 sm:px-6 lg:px-8">
        <div class="bg-white dark:bg-[#6F321F] shadow-xl rounded-lg overflow-hidden">
          <div class="p-6">
            <div class="mb-6 flex items-center justify-center">
              <IceCream class="w-10 h-10 mr-3 text-[#85311A] dark:text-white" />
              <h2 class="text-3xl font-bold text-[#85311A] dark:text-white">Our Most Loved Flavors</h2>
            </div>

            <div v-if="isLoading" class="flex justify-center items-center h-64">
              <Loader2 class="w-12 h-12 animate-spin text-[#85311A] dark:text-white" />
            </div>

            <div v-else-if="error" class="text-center text-red-600 dark:text-red-400 p-4">
              {{ error }}
            </div>

            <div v-else class="relative">
              <button @click="prevSlide" class="absolute left-0 top-1/2 transform -translate-y-1/2 bg-[#F5E9DC] dark:bg-[#9E5439] p-2 rounded-full shadow-md z-10 focus:outline-none focus:ring-2 focus:ring-[#85311A] dark:focus:ring-[#F2C6AD]" aria-label="Previous slide">
                <ChevronLeft class="w-6 h-6 text-[#85311A] dark:text-[#F2C6AD]" />
              </button>
              <button @click="nextSlide" class="absolute right-0 top-1/2 transform -translate-y-1/2 bg-[#F5E9DC] dark:bg-[#9E5439] p-2 rounded-full shadow-md z-10 focus:outline-none focus:ring-2 focus:ring-[#85311A] dark:focus:ring-[#F2C6AD]" aria-label="Next slide">
                <ChevronRight class="w-6 h-6 text-[#85311A] dark:text-[#F2C6AD]" />
              </button>

              <div class="overflow-hidden">
                <div class="flex transition-transform duration-300 ease-in-out" :style="{ transform: `translateX(-${currentSlide * 100 / itemsPerSlide}%)` }">
                  <div v-for="flavor in popularFlavors" :key="flavor.id" :class="`w-full sm:w-1/2 md:w-1/3 lg:w-1/${itemsPerSlide} flex-shrink-0 p-4`">
                    <div class="bg-[#F5E9DC] dark:bg-white rounded-lg shadow-md overflow-hidden transition-transform duration-300 hover:scale-105">
                      <img :src="flavor.image" :alt="flavor.name" class="w-full h-48 object-cover" />
                      <div class="p-4">
                        <h3 class="text-lg font-semibold text-center text-[#85311A]">{{ flavor.name }}</h3>
                        <p class="text-center text-[#87412B] mt-2">Stock: {{ flavor.stock }}</p>
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </div>

            <div class="flex justify-center mt-4">
              <button v-for="(_, index) in Array(totalSlides)" :key="index" @click="goToSlide(index)"
                class="w-3 h-3 rounded-full mx-1 cursor-pointer transition-colors duration-300 ease-in-out focus:outline-none focus:ring-2 focus:ring-[#85311A] dark:focus:ring-[#F2C6AD]"
                :class="currentSlide === index ? 'bg-[#EB9D77]' : 'bg-[#F2C6AD] dark:bg-[#6F321F]'"
                :aria-label="`Go to slide ${index + 1}`"
                :aria-current="currentSlide === index ? 'true' : 'false'">
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </AuthenticatedLayout>
</template>