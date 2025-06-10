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
    <div v-if="activeOrders.length === 0" class="text-gray-600 text-lg">Faol buyurtmalar yo‘q.</div>
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
            <span class="font-semibold">Narxi:</span> ${{ order.price.toFixed(2) }}
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
          <a :href="order.mapLink" target="_blank">Yandex Link</a>
        </div>
        <div class="flex gap-2">
          <button
            @click="finishOrder(order.id)"
            class="bg-blue-600 text-white px-5 py-2 rounded-lg hover:bg-blue-700 transition-colors focus:outline-none focus:ring-2 focus:ring-blue-500 w-full"
          >
            Tugatildi
          </button>
          <!-- <button
            @click="deleteOrder(order.id)"
            class="bg-blue-600 text-white px-5 py-2 rounded-lg hover:bg-blue-700 transition-colors focus:outline-none focus:ring-2 focus:ring-blue-500 w-full"
          >
            O'chirish
          </button> -->
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";

// You can move this to a store or a shared file if needed
const allOrders = [
  {
    id: 1,
    customer: "Ali Valiyev",
    products: ["Mahsulot A", "Mahsulot B"],
    price: 120.0,
    address: "Toshkent sh., Chilonzor tumani, 5-mavze, 12-uy",
    map: "https://www.google.com/maps?q=41.2995,69.2401&hl=uz&z=15&output=embed",
    mapLink: "https://yandex.uz/maps/-/CHSzbRO1"
  },
  {
    id: 2,
    customer: "Dilshod Karimov",
    products: ["Mahsulot C"],
    price: 55.5,
    address: "Samarqand sh., Registon ko'chasi, 21-uy",
    map: "https://www.google.com/maps?q=39.6542,66.9597&hl=uz&z=15&output=embed",
    mapLink: "https://yandex.uz/maps/-/CHSzbRO1"
  },
  {
    id: 3,
    customer: "Gulnora Xolmatova",
    products: ["Mahsulot D", "Mahsulot E", "Mahsulot F"],
    price: 200.75,
    address: "Buxoro sh., G'ijduvon tumani, 7-uy",
    map: "https://www.google.com/maps?q=40.0997,64.6833&hl=uz&z=15&output=embed",
    mapLink: "https://yandex.uz/maps/-/CHSzbRO1"
  },
];

const activeOrders = ref([]);

function loadActives() {
  const ids = JSON.parse(localStorage.getItem("actives")) || [];
  activeOrders.value = allOrders.filter((order) => ids.includes(order.id));
}

// Remove order from actives when finished
function finishOrder(id) {
  let ids = JSON.parse(localStorage.getItem("actives")) || [];
  ids = ids.filter((orderId) => orderId !== id);
  localStorage.setItem("actives", JSON.stringify(ids));
  loadActives();
}

function deleteOrder(id) {
  // Remove from actives
  let ids = JSON.parse(localStorage.getItem("actives")) || [];
  ids = ids.filter((orderId) => orderId !== id);
  localStorage.setItem("actives", JSON.stringify(ids));
  // Remove from allOrders (not persistent, just for this session)
  const idx = allOrders.findIndex(order => order.id === id);
  if (idx !== -1) allOrders.splice(idx, 1);
  loadActives();
}

onMounted(() => {
  loadActives();
});
</script>