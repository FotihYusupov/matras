<template>
  <div>
    <div>
      <nav
        class="fixed top-0 z-50 w-full bg-neutral-200 border-2 border-neutral-300"
      >
        <div class="px-3 py-3 lg:px-5 lg:pl-3">
          <div class="flex items-center justify-between">
            <div class="flex items-center justify-start rtl:justify-end">
              <!-- Sidebar toggle button for smaller screens -->
              <button
                aria-controls="logo-sidebar"
                type="button"
                class="inline-flex items-center p-2 text-sm rounded-lg sm:hidden hover:bg-gray-100 focus:outline-none focus:ring-2 focus:ring-gray-200"
              >
                <span class="sr-only">Open sidebar</span>
                <svg
                  class="w-6 h-6"
                  aria-hidden="true"
                  fill="currentColor"
                  viewBox="0 0 20 20"
                  xmlns="http://www.w3.org/2000/svg"
                >
                  <path
                    clip-rule="evenodd"
                    fill-rule="evenodd"
                    d="M2 4.75A.75.75 0 012.75 4h14.5a.75.75 0 010 1.5H2.75A.75.75 0 012 4.75zm0 10.5a.75.75 0 01.75-.75h7.5a.75.75 0 010 1.5h-7.5a.75.75 0 01-.75-.75zM2 10a.75.75 0 01.75-.75h14.5a.75.75 0 010 1.5H2.75A.75.75 0 012 10z"
                  ></path>
                </svg>
              </button>
              <!-- Logo -->
              <NuxtLink to="/" class="flex ms-2 md:me-24">
                <span
                  class="self-center text-xl font-semibold sm:text-2xl whitespace-nowrap"
                  >Dashboard</span
                >
              </NuxtLink>
            </div>
            <div class="flex items-center">
              <div class="flex items-center ms-3 relative">
                <!-- Profile picture button -->
                <button
                  type="button"
                  class="flex text-sm p-0 bg-gray-800 rounded-full focus:ring-4 focus:ring-gray-300"
                  aria-expanded="false"
                >
                  <span class="sr-only">Open user menu</span>
                  <img
                    class="w-8 h-8 rounded-full"
                    src="/profile-logo.avif"
                    alt="user photo"
                  />
                </button>
                <!-- Profile dropdown menu -->
              </div>
            </div>
          </div>
        </div>
      </nav>

      <!-- Sidebar -->
      <aside
        id="logo-sidebar"
        class="fixed top-0 left-0 z-40 w-64 h-screen pt-20 bg-neutral-200 border-r-2 border-neutral-300 transition-transform"
        :class="{
          '-translate-x-full': !isSidebarOpen,
          'sm:translate-x-0': true,
        }"
        aria-label="Sidebar"
      >
        <SideBar/>
      </aside>

      <!-- Main content -->
      <div class="h-full sm:ml-64">
        <div class="h-full w-full p-4 mt-16">
          <slot />
        </div>
      </div>
    </div>
  </div>
</template>


<script setup>
import { onMounted } from "vue";

  onMounted(() => {
    const actives = JSON.parse(localStorage.getItem("actives")) || [];
    if(actives.length > 0) {
      navigateTo("/actives");
    }
  })
</script>