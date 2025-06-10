<template>
  <div class="h-full px-3 pb-4 overflow-y-auto">
    <ul class="space-y-2 font-medium">
      <li v-for="item in data" v-bind:key="item.id">
        <NuxtLink
          :to="item.link"
          class="flex mb-1 items-center p-2 rounded-lg hover:bg-neutral-400 group transition"
          active-class="bg-neutral-300"
          @click="toggleSidebar"
        >
          <img :src="`/images/${item.icon}`" >
          <span class="ms-3">{{ item.name }}</span>
        </NuxtLink>
      </li>
    </ul>
  </div>
</template>

<script setup>
  const props = defineProps(['admin']);

  const data = [
    {
      id: 1,
      name: "Buyurtmalar",
      link: "/",
      icon: "user.svg",
    },
    {
      id: 2,
      name: "Faol buyurtmalar",
      link: "/actives",
      icon: "server.svg"
    }
  ]
</script>
