<script setup>
defineProps({
  totalPrice: Number
})

const emit = defineEmits(['openDrawer'])
</script>

<template>
  <header class="flex justify-between border-b border-slate-300 px-8 py-8">
    <router-link to="/">
      <div class="flex items-center cursor-pointer gap-3 text-grey-500 hover:text-black">
        <img src="/logo.png" alt="logo" class="w-20" />
        <div>
          <h2 class="text-xl font-vold uppercase">Reading room</h2>
        </div>
      </div>
    </router-link>

    <div>
      <ul class="frex items-center gap-10">
        <router-link to="/categories">
          <li class="flex items-center cursor-pointer gap-3 text-grey-500 hover:text-black">
            <img src="/cati.png" alt="Cart" class="w-10" />
            <h2 class="text-3xl font-normal mb-8 relative">
              <span
                class="absolute top-0 left-0 w-full h-full bg-gradient-to-r from-pink-400 via-purple-500 to-indigo-500 bg-clip-text"
                style="
                  text-align: center;
                  font-size: 2rem;
                  white-space: nowrap; /* Убрать перенос текста */
                "
              >
                Каталог книг
              </span>
            </h2>
          </li>
        </router-link>
      </ul>
    </div>

    <ul class="frex items-center gap-10">
      <li
        @click="() => emit('openDrawer')"
        class="flex items-center cursor-pointer gap-3 text-grey-500 hover:text-black"
      >
        <img src="/cart.svg" alt="Cart" />
        <span>{{ totalPrice }} руб.</span>
      </li>
    </ul>
  </header>
</template>
<style scoped>
.header-nav {
  display: flex;
  align-items: center;
  justify-content: flex-end;
  gap: 16px;
}

.header-nav a {
  text-decoration: none;
  color: #333;
  font-size: 30px;
  font-weight: 500;
  transition: color 0.3s;
}

.header-nav a:hover {
  color: #ff69b4;
}
</style>
