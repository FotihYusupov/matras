<template>
  <div class="container mx-auto p-6">
    <div class="mb-4 block flex items-center justify-center space-x-4">
      <NuxtLink
        to="/" 
        class="block text-lg font-semibold hover:text-blue-600 transition-colors"
        active-class="text-blue-600 underline"
      >
        Buyurtmalar
      </NuxtLink>
      <NuxtLink 
        to="/actives" 
        class="text-lg font-semibold hover:text-blue-600 transition-colors"
        active-class="text-blue-600 underline"
      >
        Faol Buyurtmalar
      </NuxtLink>
    </div>
    <h1 class="text-3xl font-bold text-gray-800 mb-6">Buyurtmalar</h1>
    <div v-if="filteredOrders.length" class="grid gap-4 sm:grid-cols-2 xl:grid-cols-3">
      <div
        v-for="order in filteredOrders"
        :key="order.id"
        class="bg-white border border-gray-200 rounded-lg shadow-md p-4 mb-4 hover:shadow-lg transition-shadow flex flex-col justify-between"
      >
        <div class="space-y-2 mb-4">
          <p class="text-lg">
            <span class="font-semibold">Buyurtma raqami:</span> {{ order.id }}
          </p>
          <p class="text-lg">
            <span class="font-semibold">Mijoz:</span> {{ order.customer }}
          </p>
        </div>
        <div class="mb-4">
          <p class="font-semibold text-gray-700 mb-1">Mahsulotlar:</p>
          <ul class="list-disc list-inside text-gray-700">
            <li v-for="item in order.products" :key="item">{{ item }}</li>
          </ul>
        </div>
        <div class="space-y-1 mb-4">
          <p class="text-lg">
            <span class="font-semibold">Narxi:</span> ${{ order.price }}
          </p>
          <p class="text-lg">
            <span class="font-semibold">Manzil:</span> {{ order.address }}
          </p>
        </div>
        <button
          @click="openModal(order)"
          class="bg-blue-600 text-white px-5 py-2 rounded-lg hover:bg-blue-700 transition-colors focus:outline-none focus:ring-2 focus:ring-blue-500 w-full"
        >
          Batafsil
        </button>
      </div>
    </div>
    <div v-else class="text-center text-gray-600">
      <p>Aktiv bo'lmagan buyurtmalar yo'q.</p>
    </div>

    <!-- Modal -->
    <Transition name="modal">
      <div
        v-if="showModal"
        class="fixed inset-0 z-50 flex items-center justify-center bg-gray-50"
      >
        <!-- Modal overlay with blur effect -->
        <div
          class="absolute inset-0 transition-opacity"
          @click="closeModal"
        ></div>
        <!-- Modal content -->
        <div
          class="relative z-10 bg-white rounded-xl shadow-2xl max-w-md w-full p-6 transform transition-all animate-fadeIn"
        >
          <button
            @click="closeModal"
            class="absolute top-3 right-3 text-gray-500 hover:text-gray-700 focus:outline-none"
            aria-label="Yopish"
          >
            <svg
              class="w-6 h-6"
              fill="none"
              stroke="currentColor"
              viewBox="0 0 24 24"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="M6 18L18 6M6 6l12 12"
              />
            </svg>
          </button>
          <h2 class="text-2xl font-bold text-gray-800 mb-4">
            Buyurtma tafsilotlari
          </h2>
          <div class="space-y-3 text-gray-700 mb-4">
            <p>
              <span class="font-semibold">Buyurtma raqami:</span>
              {{ selectedOrder.id }}
            </p>
            <div>
              <span class="font-semibold">Mahsulotlar:</span>
              <ul class="list-disc list-inside ml-4">
                <li v-for="item in selectedOrder.products" :key="item">
                  {{ item }}
                </li>
              </ul>
            </div>
            <p>
              <span class="font-semibold">Narxi:</span>
              ${{ selectedOrder.price && selectedOrder.price.toFixed
                ? selectedOrder.price.toFixed(2)
                : "" }}
            </p>
            <p>
              <span class="font-semibold">Manzil:</span>
              {{ selectedOrder.address }}
            </p>
            <p>
              <span class="font-semibold">Mijoz:</span>
              {{ selectedOrder.customer }}
            </p>
          </div>
          <div v-if="selectedOrder.map" class="gets a little bit of a boost">
            <span class="font-semibold text-gray-700 mb-2 block"
              >Manzil (Google xaritada):</span
            >
            <iframe
              :src="selectedOrder.map"
              width="100%"
              height="220"
              style="border:0; border-radius: 12px"
              allowfullscreen=""
              loading="lazy"
              referrerpolicy="no-referrer-when-downgrade"
            ></iframe>
          </div>
          <div>
            <a 
              @click="startDelivery(selectedOrder.id)"
              :href="selectedOrder.mapLink"
              class="block mt-2 text-center bg-blue-600 text-white px-5 py-2 rounded-lg hover:bg-blue-700 transition-colors focus:outline-none focus:ring-2 focus:ring-blue-500 w-full">
              Boshlash
            </a>
          </div>
        </div>
      </div>
    </Transition>
  </div>
</template>

<script setup>
import { navigateTo } from '#app';
import { ref, computed, onMounted } from 'vue';

// Initialize orders as a reactive reference
const orders = ref([]);

// State for modal
const showModal = ref(false);
const selectedOrder = ref({});

// Fetch orders from the API
async function fetchOrders() {
  try {
    const response = await fetch('https://matras.kingsman.boutique/orders');
    if (!response.ok) {
      throw new Error(`HTTP error! Status: ${response.status}`);
    }
    const data = await response.json();
    
    // Map API data to the template's expected order structure
    orders.value = data.map(order => ({
      id: order._id,
      customer: `${order.firstName} ${order.lastName}`,
      products: [`${order.productType} (Eni: ${order.eniga}sm, Boyi: ${order.boyiga}sm, ${order.quantity} dona)`],
      price: calculatePrice(order),
      address: order.address || 'Toshkent sh., Chilonzor tumani, 5-mavze, 12-uy',
      map: order.map || 'https://www.google.com/maps?q=41.2995,69.2401&hl=uz&z=15&output=embed',
      mapLink: order.mapLink || 'https://yandex.uz/maps/-/CHSzbRO1',
    }));
  } catch (error) {
    console.error('Error fetching orders:', error);
  }
}

// Example price calculation
function calculatePrice(order) {
  return (order.quantity * 50).toFixed(2); // Example: $50 per unit
}

// Fetch orders when component is mounted
onMounted(() => {
  fetchOrders();
});

// Compute filtered orders excluding active ones
const filteredOrders = computed(() => {
  const activeOrders = JSON.parse(localStorage.getItem('actives')) || [];
  return orders.value.filter(order => !activeOrders.includes(order.id));
});

function openModal(order) {
  selectedOrder.value = order;
  showModal.value = true;
}

function closeModal() {
  showModal.value = false;
  selectedOrder.value = {};
}

function startDelivery(id) {
  const activeOrders = JSON.parse(localStorage.getItem('actives')) || [];
  activeOrders.push(id);
  localStorage.setItem('actives', JSON.stringify(activeOrders));
  closeModal();
  navigateTo('/actives');
}
</script>

<style scoped>
.modal-enter-active,
.modal-leave-active {
  transition: opacity 0.25s;
}
.modal-enter-from,
.modal-leave-to {
  opacity: 0;
}
@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(24px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
.animate-fadeIn {
  animation: fadeIn 0.25s;
}
</style>