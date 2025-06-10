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
    <h1 class="text-3xl font-bold text-gray-800 mb-6">Faol buyurtmalar</h1>
    <div v-if="activeOrders.length === 0" class="text-gray-600 text-lg">
      Faol buyurtmalar yo‘q.
    </div>
    <div class="grid gap-4 sm:grid-cols-2 xl:grid-cols-3" v-else>
      <div
        v-for="order in activeOrders"
        :key="order.id"
        class="bg-white border border-gray-200 rounded-lg shadow-md p-4 hover:shadow-lg transition-shadow flex flex-col justify-between"
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
        <div v-if="order.map" class="mb-4">
          <span class="font-semibold text-gray-700 mb-2 block">Manzil (Google xaritada):</span>
          <iframe
            :src="order.map"
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
            :href="order.mapLink"
            target="_blank"
            class="block text-center bg-blue-600 text-white px-5 py-2 rounded-lg hover:bg-blue-700 transition-colors focus:outline-none focus:ring-2 focus:ring-blue-500 mb-2"
          >
            Yandex xaritada ochish
          </a>
        </div>
        <div class="flex gap-2">
          <button
            @click="finishOrder(order.id)"
            class="bg-blue-600 text-white px-5 py-2 rounded-lg hover:bg-blue-700 transition-colors focus:outline-none focus:ring-2 focus:ring-blue-500 w-full"
          >
            Tugatildi
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue';

// Initialize orders as a reactive reference
const orders = ref([]);

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
  return (order.quantity * 50) // Example: $50 per unit
}

// Compute active orders based on localStorage
const activeOrders = computed(() => {
  const activeOrderIds = JSON.parse(localStorage.getItem('actives')) || [];
  return orders.value.filter(order => activeOrderIds.includes(order.id));
});

// Remove order from actives when finished
function finishOrder(id) {
  let activeOrderIds = JSON.parse(localStorage.getItem('actives')) || [];
  activeOrderIds = activeOrderIds.filter(orderId => orderId !== id);
  localStorage.setItem('actives', JSON.stringify(activeOrderIds));
}

// Fetch orders when component is mounted
onMounted(() => {
  fetchOrders();
});
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