<script setup lang="ts">
import { ref } from 'vue';
import AppButton from './AppButton.vue';

interface Props {
  currentRoute: string;
  activeVariant?: 'modern' | 'traditional';
}

withDefaults(defineProps<Props>(), {
  activeVariant: 'modern'
});

const emit = defineEmits<{
  (e: 'navigate', route: string, id?: number): void;
}>();

const isMobileMenuOpen = ref(false);

const handleMobileNavigate = (route: string) => {
  emit('navigate', route);
  isMobileMenuOpen.value = false;
};
</script>

<template>
  <header 
    :class="activeVariant === 'traditional' 
      ? 'bg-(--color-crema-newen)/90 backdrop-blur-md shadow-sm sticky top-0 z-50 border-b border-(--color-gold-newen)/10' 
      : 'bg-white shadow-sm sticky top-0 z-50'"
  >
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 h-20 flex items-center justify-between">
      
      <!-- Logo -->
      <div class="flex items-center gap-3 cursor-pointer" @click="emit('navigate', 'home')">
        <img 
          src="/images/logo.webp" 
          alt="Newen Leufu Logo" 
          class="h-12 w-auto object-contain mix-blend-multiply" 
          :class="activeVariant === 'traditional' ? 'filter sepia' : ''"
        />
      </div>
      
      <!-- Desktop Navigation -->
      <nav 
        class="hidden md:flex gap-8 text-sm"
        :class="activeVariant === 'traditional' ? 'font-bold text-gray-800 tracking-wide uppercase' : 'font-semibold text-gray-600'"
      >
        <a href="#" @click.prevent="emit('navigate', 'home')" :class="currentRoute === 'home' ? 'text-(--color-green-newen) border-b-2 border-(--color-green-newen) py-2' : 'hover:text-(--color-green-newen) py-2 transition-colors'">Inicio</a>
        <a href="#" @click.prevent="emit('navigate', 'catalog')" :class="currentRoute === 'catalog' ? 'text-(--color-green-newen) border-b-2 border-(--color-green-newen) py-2' : 'hover:text-(--color-green-newen) py-2 transition-colors'">Catálogo</a>
        <a href="#" @click.prevent="emit('navigate', 'about')" :class="currentRoute === 'about' ? 'text-(--color-green-newen) border-b-2 border-(--color-green-newen) py-2' : 'hover:text-(--color-green-newen) py-2 transition-colors'">Quiénes Somos</a>
        <a href="#" @click.prevent="emit('navigate', 'contact')" :class="currentRoute === 'contact' ? 'text-(--color-green-newen) border-b-2 border-(--color-green-newen) py-2' : 'hover:text-(--color-green-newen) py-2 transition-colors'">Contacto</a>
      </nav>
      
      <!-- Desktop Actions -->
      <div class="hidden md:flex items-center gap-4">
        <span class="text-sm font-bold text-gray-500 cursor-pointer hover:text-gray-900 border-r border-gray-300 pr-4">ES / EN</span>
        <AppButton size="sm" :active-variant="activeVariant" @click="emit('navigate', 'checkout')">
          Reservar
        </AppButton>
      </div>

      <!-- Hamburger Button (Mobile) -->
      <div class="flex items-center gap-3 md:hidden">
        <span class="text-xs font-bold text-gray-500">ES / EN</span>
        <button 
          @click="isMobileMenuOpen = !isMobileMenuOpen" 
          class="text-gray-600 hover:text-gray-900 focus:outline-none p-2 rounded-lg hover:bg-gray-100 transition-colors"
          aria-label="Abrir Menú"
        >
          <svg class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path v-if="!isMobileMenuOpen" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16M4 18h16" />
            <path v-else stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
          </svg>
        </button>
      </div>
    </div>

    <!-- Mobile Dropdown Menu (REQUERIMIENTOS: Adaptabilidad para móviles) -->
    <div 
      v-if="isMobileMenuOpen" 
      class="md:hidden border-t"
      :class="activeVariant === 'traditional' 
        ? 'border-(--color-gold-newen)/10 bg-(--color-crema-newen)/95 backdrop-blur-md shadow-inner animate-fade-in' 
        : 'border-gray-150 bg-white shadow-inner animate-fade-in'"
    >
      <div 
        class="px-4 pt-3 pb-6 space-y-3 flex flex-col"
        :class="activeVariant === 'traditional' ? 'uppercase tracking-wider font-bold text-gray-800' : 'font-semibold text-gray-600'"
      >
        <a 
          href="#" 
          @click.prevent="handleMobileNavigate('home')" 
          class="px-3 py-2 rounded-xl text-sm transition-colors"
          :class="currentRoute === 'home' ? 'bg-green-50 text-(--color-green-newen)' : 'hover:bg-gray-50'"
        >
          Inicio
        </a>
        <a 
          href="#" 
          @click.prevent="handleMobileNavigate('catalog')" 
          class="px-3 py-2 rounded-xl text-sm transition-colors"
          :class="currentRoute === 'catalog' ? 'bg-green-50 text-(--color-green-newen)' : 'hover:bg-gray-50'"
        >
          Catálogo
        </a>
        <a 
          href="#" 
          @click.prevent="handleMobileNavigate('about')" 
          class="px-3 py-2 rounded-xl text-sm transition-colors"
          :class="currentRoute === 'about' ? 'bg-green-50 text-(--color-green-newen)' : 'hover:bg-gray-50'"
        >
          Quiénes Somos
        </a>
        <a 
          href="#" 
          @click.prevent="handleMobileNavigate('contact')" 
          class="px-3 py-2 rounded-xl text-sm transition-colors"
          :class="currentRoute === 'contact' ? 'bg-green-50 text-(--color-green-newen)' : 'hover:bg-gray-50'"
        >
          Contacto
        </a>
        <div class="border-t border-gray-100 pt-3">
          <AppButton class="w-full" :active-variant="activeVariant" @click="handleMobileNavigate('checkout')">
            Reservar Ahora
          </AppButton>
        </div>
      </div>
    </div>
  </header>
</template>
